---
title: "Secure Session Workflows with AWS KMS-Signed JWTs (JWS)"
date: 2026-05-08
tags:
    - KMS
    - JWT
    - Security
author: Irodotos Apostolou
---

## Setting the scene

How do you trust a client’s session during upload without re-running expensive validation logic or exposing yourself to tampering?

In this post, we’ll walk through a production-grade pattern using:

- Short-lived signed JWTs (JWS)
- AWS KMS for signing & verification
- A token chaining model that enforces trust across steps

## The Goal

### We wanted to guarantee:

- A session is validated exactly once
- Uploads are cryptographically bound to that validation
- No reliance on:
    - Long-lived credentials
    - Re-validating session ownership on every request

## The Problem

### Our session lifecycle has two phases:

#### 1. Validation

Client proves ownership of a key and receives:

- Session state
- Progress info

#### 2. Upload

Client submits gameplay events tied to that session

## The Naive Approach
```
POST /upload_session
→ Accept key + session_key
→ Re-run validation logic or just accept the data
```

### Why this fails:

- Tight coupling between upload & validation
  - Now the upload service must answer questions like:
    - Does this session belong to this key?
    - Was the session already validated?
    - Is the session still active?
    - Was it already completed?

  - Over time this becomes difficult to maintain because:
    - validation rules get duplicated
    - services become dependent on shared state
    - performance degrades under repeated checks

- Vulnerable to session key substitution
  - If the backend only checks:
    - “does this session exist?” => then uploads may be accepted for the wrong session.

- Easy to accept events for unauthorized sessions
  - Without a signed validation token, the upload endpoint has no proof that:
    - validation actually happened
    - the session was approved
    - the uploader is authorized for that session

  - This makes it easy for clients to:
    - replay old uploads
    - fabricate requests
    - upload directly without validation
    - target sessions they should not control

  - Example
    1. Client validates Session A
    2. Client modifies request
    3. Uploads events for Session B
    4. Server accepts upload


## The Key Idea

*“If validation already proved this session is valid — just sign that fact.”*

### Instead of re-validating:

- Issue a signed token after validation
- Require that token for upload

## The Token Chain Architecture

We introduce **three endpoints** and **two token types**:

## High-Level Flow

![high-level-flow](/assets/images/posts/secure-session-kms-jwt-with-diagrams/high-level-flow.png)

## Step-by-Step Breakdown

### 1) `GET /prevalidate/key:key`

#### Purpose

- Validate the key
- Return session state only (no session creation)

#### Output

**A prevalidation token**

Example Claims
```json
{
  "key": "k_abc123",
  "sessionStatus": "NotStarted" | "Started" | "Completed",
  "timestamp": "2024-01-15T10:00:00Z"
}
```

### 2) Validate

`POST /validate`

#### Input
- Bearer token: **prevalidation JWT**

#### What happens
- Verify token via KMS
- Enforce token type (cty = prevalidation)
- Run validation logic
- Create or resume session

#### Output

**A session token**
```json
{
  "sessionKey": "sk_xyz789",
  "key": "kk_abc123",
  "timestamp": "2024-01-15T10:01:00Z"
}
```

### 3) Upload

`POST /upload_session`

#### Input
- Bearer token: **validation JWT**

#### What happens
- Verify token via KMS
- Extract sessionKey from token
- Insert events using that key

Request Example
```json
{
  "key": "kk_abc123",
  "session_events": {
    "data": [
      ...
    ]
  }
}
```

## Token Design
### Two Token Types

| Property   | Prevalidation     | Validation     |
| ---------- | ----------------- | ----------- |
| `cty`      | `"prevalidation"` | `"validation"` |
| sessionKey | Optional          | Required    |
| Used by    | validate          | upload      |

## JWT Structure
```text
Header:  { "alg": "RS256", "kid": "<kms-key-id>", "cty": "session" }
Payload: { ...claims, "iat": ..., "exp": ... }
```

## Signing Flow

![signingflow](/assets/images/posts/secure-session-kms-jwt-with-diagrams/signing-flow.png)

### We use:

- RSA asymmetric key
- Algorithm: RSASSA_PKCS1_V1_5_SHA_256 (→ JWT RS256)
### Flow
- Build header.payload
- Send to KMS Sign
- Encode signature
- Construct final JWT

```scala
private def signString(keyId: String, unsignedString: String): App[String] =
  RIO.ask[Env].mapIO { env =>
    IO.fromCompletableFuture(IO(
      env.kmsClient.sign(
        SignRequest.builder()
          .keyId(keyId)
          .message(SdkBytes.fromUtf8String(unsignedString))
          .signingAlgorithm(SigningAlgorithmSpec.RSASSA_PKCS1_V1_5_SHA_256)
          .build()
      ))).map(_.signature())
       .map(sdkBytes => JwtBase64.encodeString(sdkBytes.asByteArray()))
  }
```

## Verification with AWS KMS

![verificationFlow](/assets/images/posts/secure-session-kms-jwt-with-diagrams/verification-with-kms.png)

### Steps:
- Decode JWT (skip signature, enforce expiration)
- Validate kid
- Call KMS Verify
- Parse claims by cty

```scala
private def verifyWithKms(keyId: String, signedSessionString: String, signature: String): App[Unit] =
  RIO.ask[Env].mapIO { env =>
    val unsignedTokenString = signedSessionString.substring(0, signedSessionString.lastIndexOf('.'))
    IO.fromCompletableFuture(IO(
      env.kmsClient.verify(
        VerifyRequest.builder()
          .keyId(keyId)
          .message(SdkBytes.fromUtf8String(unsignedTokenString))
          .signature(SdkBytes.fromByteArray(JwtBase64.decode(signature)))
          .signingAlgorithm(SigningAlgorithmSpec.RSASSA_PKCS1_V1_5_SHA_256)
          .build()
      )
    )).flatMap { vr =>
      if (vr.signatureValid().booleanValue()) IO.unit
      else IO.raiseError(InvalidSessionVerification("The token did not pass signature verification"))
    }
  }
```

## Error Handling
```json
{
  "result": "failure",
  "code": 1043,
  "message": "Error authenticating session token"
}
```

| Code | Meaning          |
| ---- | ---------------- |
| 1043 | Invalid token    |
| 1017 | Key expired   |
| 1011 | Key not found |
| 1041 | Duplicate upload |
| 1042 | Session locked   |
| 1044 | Wrong type of token   |

## Final Architecture Insight

![finalArchitectureFlow](/assets/images/posts/secure-session-kms-jwt-with-diagrams/final-architecture-flow.png)

## Why This Pattern Works
- Eliminates re-validation overhead
- Prevents session tampering
- Cryptographic guarantees
- Scales cleanly across services

## Summary
- Validation happens once
- Trust is enforced via signed tokens
- Upload becomes:
  - Stateless
  - Fast
  - Secure