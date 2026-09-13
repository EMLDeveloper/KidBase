# ADR-004 — Use JWT for Authentication

**Date:** 2026-09-12
**Status:** Accepted

## Context

KidBase needs a secure, stateless authentication mechanism that works across the React frontend and Spring Boot backend.

## Decision

Use JWT (JSON Web Tokens) with Spring Security for authentication and authorization.

## Reasons

- Stateless — no server-side session storage required
- Works naturally with REST APIs
- Carries role information inside the token
- Industry standard for modern web applications
- Spring Security supports JWT-based resource server authentication well

## Important Clarification

Spring Security does not manage the full JWT lifecycle automatically. The application is responsible for designing how tokens are:

- Issued and signed (on login)
- Validated (on every protected request)
- Stored (client-side, handled by the frontend)
- Refreshed (future version)
- Revoked (future version)

## Trade-offs

- Tokens cannot be invalidated before expiration without additional infrastructure
- Token size is larger than a session ID
- Requires secure storage on the client side

## MVP Decision

- Access tokens are short-lived to limit exposure
- Logout is handled on the frontend by discarding the token
- Token blacklisting and refresh token revocation are deferred to a future version
