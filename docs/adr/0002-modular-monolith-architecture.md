# ADR-002 — Use Modular Monolith Architecture

**Date:** 2026-09-12
**Status:** Accepted

## Context

KidBase needs an architecture that is manageable for a solo developer, demonstrates professional software design, and can evolve as the system grows.

## Decision

Build KidBase as a Modular Monolith — one Spring Boot application organized into clean, independent modules.

## Reasons

- Simpler to develop, test, and deploy as a solo developer
- Each module is loosely coupled and independently maintainable
- Avoids the operational complexity of microservices
- Industry-recommended starting point before microservices
- Modules can be extracted into microservices in the future

## Trade-offs

- Cannot scale individual modules independently
- All modules share one deployment
- A critical failure could affect the entire application

## Future Path

When KidBase requires independent scaling, modules are designed to be extracted into microservices with minimal refactoring.
