# ADR-001 — Use Java and Spring Boot as the Backend Framework

**Date:** 2026-09-12
**Status:** Accepted

## Context

KidBase needs a backend framework that is production-proven, widely used in enterprise software, and aligns with the target skill set for a junior Java backend developer role.

## Decision

Use Java with Spring Boot as the core backend framework.

## Reasons

- Industry standard for enterprise backend development
- Large ecosystem of libraries and integrations
- Spring Security handles authentication and authorization
- Spring Data JPA simplifies database access
- Directly maps to junior Java backend developer job requirements

## Trade-offs

- More verbose than frameworks like Node.js or Django
- Longer startup time compared to lightweight frameworks
- Higher learning curve for a new developer
