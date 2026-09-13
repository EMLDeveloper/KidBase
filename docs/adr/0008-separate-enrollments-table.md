# ADR-008 — Use a Separate `enrollments` Table

**Date:** 2026-09-12
**Status:** Accepted

## Context

A child's enrollment history needs to be tracked over time. A child could leave the daycare and return in a future year, requiring multiple enrollment periods.

## Decision

Store enrollment periods in a separate `enrollments` table linked to `children` rather than embedding enrollment data directly in the `children` table.

## Reasons

- Supports multiple enrollment periods per child
- Preserves complete enrollment history
- Allows different enrollment types per period
- Cleaner data model

## Trade-offs

- Requires an additional JOIN to get enrollment info
- Slightly more complex enrollment queries
