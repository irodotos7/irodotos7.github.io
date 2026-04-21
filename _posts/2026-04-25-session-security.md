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
