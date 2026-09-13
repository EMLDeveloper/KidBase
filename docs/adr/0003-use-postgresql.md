# ADR-003 — Use PostgreSQL as the Database

**Date:** 2026-09-12
**Status:** Accepted

## Context

KidBase manages many connected entities — children, guardians, staff, enrollments, attendance, documents, and notifications — that have well-defined relationships and require data integrity.

## Decision

Use PostgreSQL as the relational database.

## Reasons

- Relational model fits the connected nature of the data
- Strong support for foreign keys, constraints, and transactions
- Industry standard for enterprise applications
- Excellent Spring Data JPA and Hibernate integration
- Managed service available on AWS RDS for future deployment

## Trade-offs

- Requires schema design upfront
- Less flexible than NoSQL for unstructured data
- Schema migrations require careful management
