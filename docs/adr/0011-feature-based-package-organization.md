# ADR-011 — Use Feature-Based Package Organization

**Date:** 2026-09-12
**Status:** Accepted

## Context

Spring Boot applications can be organized in two common ways — by technical layer or by feature. This decision defines which approach KidBase uses and why.

## Decision

Organize the KidBase codebase by feature module, not by technical layer.

### Feature-based (chosen)

```
com.kidbase/
├── auth/
├── child/
├── attendance/
├── staff/
├── guardian/
├── enrollment/
├── document/
└── notification/
```

### Layer-based (rejected)

```
com.kidbase/
├── controllers/
├── services/
├── repositories/
└── entities/
```

## Reasons

- Each module is self-contained and independently maintainable
- Easier to navigate — everything related to a feature lives in one place
- Naturally maps to the business domain
- Aligns with the Modular Monolith architecture decision in [ADR-002](0002-modular-monolith-architecture.md)
- Prepares modules for future extraction into separate services if ever needed

## Implementation

Each feature package contains its own layers, for example:

```
child/
├── Child.java              (entity)
├── ChildRepository.java    (repository)
├── ChildService.java       (service)
├── ChildController.java    (controller)
└── ChildDTO.java           (data transfer object)
```

## Trade-offs

- Less familiar to developers who learned layer-based organization first
- Cross-cutting concerns (security, exceptions, configuration) still need a shared package:

```
com.kidbase/
├── config/
├── exception/
├── security/
└── common/
```
