---
title: "🔐 Secure Session Workflows with AWS KMS-Signed JWTs"
date: 2026-04-21
tags:
    - KMS
    - JWT
    - Security
author: Irodotos Apostolou
---

## Setting the scene

How do you trust a client’s session during upload without re-running expensive validation logic or exposing yourself to tampering?

In this post, we’ll walk through a production-grade pattern using:

- Short-lived JWTs
- AWS KMS for signing & verification
- A token chaining model that enforces trust across steps

## 🎯 The Goal

We wanted to guarantee:

- A session is validated exactly once
- Uploads are cryptographically bound to that validation
- No reliance on:
    - Long-lived credentials
    - Re-validating session ownership on every request

## ⚠️ The Problem

Our session lifecycle has two phases:

1. Validation

Client proves ownership of a player_key and receives:

- Session state
- Available levels
- Progress info

2. Upload

Client submits gameplay events tied to that session

## ❌ The Naive Approach
```
POST /upload_session
→ Accept player_key + session_key
→ Re-run validation logic
```

Why this fails:

🔗 Tight coupling between upload & validation

🔓 Vulnerable to session key substitution

⚠️ Easy to accept events for unauthorized sessions

## 💡 The Key Idea

“If validation already proved this session is valid — just sign that fact.”

Instead of re-validating:

- Issue a signed token after validation
- Require that token for upload

## The Token Chain Architecture

We introduce **three endpoints** and **two token types**:

## High-Level Flow

![high-level-flow](/assets/images/posts/secure-session-kms-jwt-with-diagrams/high-level-flow.png)

## Step-by-Step Breakdown

### 1) `GET /prevalidate/key:key`

#### Purpose

- Validate player key
- Return session state only (no session creation)

#### Output

**A preassessment token**

Example Claims
```
{
  "key": "k_abc123",
  "sessionKey": null,
  "sessionStatus": "NotStarted" | "Started" | Completed,
  "previouslyCompleted": [],
  "timestamp": "2024-01-15T10:00:00Z"
}
```

*previouslyCompleted allows reuse of past sessions.*

### 2) Validate

`POST /v2/session/validate`

#### Input
- Bearer token: preassessment JWT

#### What happens
- Verify token via KMS
- Enforce token type (cty = preassessment)
- Run validation logic
- Create or resume session

#### Output

**A session token**
```
{
  "sessionKey": "sk_xyz789",
  "key": "kk_abc123",
  "reused", True | False,
  "timestamp": "2024-01-15T10:01:00Z"
}
```

### 3) Upload

`POST /v2/upload_session`

#### Input
- Bearer token: session JWT

#### What happens
- Verify token via KMS
- Extract sessionKey from token
- Insert events using that key

Request Example
```
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

| Property   | Preassessment     | Session     |
| ---------- | ----------------- | ----------- |
| `cty`      | `"preassessment"` | `"session"` |
| sessionKey | Optional          | Required    |
| Used by    | validate          | upload      |

## JWT Structure
```
Header:  { "alg": "RS256", "kid": "<kms-key-id>", "cty": "session" }
Payload: { ...claims, "iat": ..., "exp": ... }
```

## Signing Flow

![signingflow](/assets/images/posts/secure-session-kms-jwt-with-diagrams/signing-flow.png)