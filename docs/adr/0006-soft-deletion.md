# ADR-006 — Use Soft Deletion Instead of Hard Delete

**Date:** 2026-09-12
**Status:** Accepted

## Context

KidBase manages sensitive business records — children, enrollments, attendance, documents — that have legal, operational, and historical value. Physically deleting records risks losing data that may be needed later.

## Decision

KidBase never physically deletes important business records. Instead it uses status fields to deactivate or archive records.

## Reasons

- Preserves historical records for auditing and compliance
- Prevents accidental permanent data loss
- Maintains referential integrity across related tables
- Reflects how real daycare businesses manage records

## Implementation

| Entity | Field | Inactive Value |
| --- | --- | --- |
| Child | enrollment_status | WITHDRAWN |
| Staff | employment_status | INACTIVE |
| User | is_active | FALSE |
| Enrollment | status | COMPLETED |
| Document | status | ARCHIVED |

## Trade-offs

- Database grows larger over time
- Queries must filter by status to exclude inactive records
- Slightly more complex query logic
