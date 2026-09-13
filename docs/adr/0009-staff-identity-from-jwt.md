# ADR-009 — Determine Staff Identity From JWT on Attendance

**Date:** 2026-09-12
**Status:** Accepted

## Context

The attendance check-in endpoints need to record which staff member processed each check-in and check-out. The initial design passed `staff_id` in the request body, which is a security risk.

## Decision

The backend determines `checked_in_by` and `checked_out_by` from the authenticated JWT token, never from the client request body.

## Reasons

- Never trust client-supplied identity
- Prevents staff from falsely attributing attendance records to other users
- Follows the principle of never trusting the client

## Trade-offs

- All attendance actions depend on a valid authenticated security context — an expired or invalid token will prevent the action entirely
- Admin corrections to attendance records (fixing a wrong check-in) require separate privileged endpoints that bypass the JWT identity rule safely
