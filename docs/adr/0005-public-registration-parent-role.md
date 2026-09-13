# ADR-005 — Public Registration Always Creates PARENT Role

**Date:** 2026-09-12
**Status:** Accepted

## Context

The API needs a public registration endpoint for parents while preventing unauthorized users from self-assigning elevated roles like ADMIN or STAFF.

## Decision

The public `/auth/register` endpoint always creates a PARENT account regardless of any role value sent in the request. ADMIN and STAFF accounts are created exclusively through protected admin endpoints.

## Reasons

- Prevents role escalation attacks
- Follows the principle of least privilege
- Keeps the public surface of the API minimal and safe

## Trade-offs

- Admins must manually create staff accounts
- Slightly more administrative overhead for onboarding
