# ADR-010 — Build in Phases, Not All at Once

**Date:** 2026-09-12
**Status:** Accepted

## Context

KidBase is built by a solo developer learning while building. Attempting to configure Docker, AWS, and other infrastructure from day one would add complexity before the core application is validated.

## Decision

Build KidBase incrementally in defined phases:

- **Phase 1 — Core application:** Spring Boot + PostgreSQL
- **Phase 2 — Containerization:** Docker
- **Phase 3 — Cloud deployment:** AWS infrastructure as needed
- **Phase 4 — CI/CD:** GitHub Actions
- **Phase 5 — Architecture evolution:** Evaluate caching, messaging, microservices, Kubernetes, or other infrastructure only if real requirements justify them

## Reasons

- Validate the core application before adding infrastructure complexity
- Each phase produces a working, deployable system
- Reduces cognitive overload
- Mirrors how real engineering teams build systems
- Architecture should follow requirements, not assumptions about future scale

## Trade-offs

- Cloud features like document storage and SMS notifications are deferred to Phase 3
- Local file storage used temporarily in Phase 1

See [10-architecture-design.md](../10-architecture-design.md#development-phases) for the full phase breakdown.
