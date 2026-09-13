# KidBase — Architecture Design

## Architecture Style

KidBase is built as a **Modular Monolith** — a single Spring Boot application internally organized into clean, independent modules. Each module represents a core business domain and is fully separated in code, making the system maintainable, scalable, and ready to evolve as the project grows.

## Why Modular Monolith

- Simpler to develop, test, and deploy as a solo developer
- Each module is independent and loosely coupled
- One deployment instead of managing multiple services
- The core application can be built and validated first
- Infrastructure and DevOps layers are added incrementally
- Recommended industry path before adopting microservices

## Development Philosophy

KidBase is built in phases. The priority is always a working, well-tested core application first. Infrastructure, cloud, and DevOps are added only after the core is stable. See [Development Phases](#development-phases) below.

## Technology Stack

| Layer | Technology |
| --- | --- |
| **Frontend** | React |
| **Backend** | Java, Spring Boot, Maven |
| **Database** | PostgreSQL |
| **Persistence** | Spring Data JPA, Hibernate |
| **Security** | Spring Security, JWT |
| **API Documentation** | Swagger / OpenAPI |
| **Testing** | JUnit, Mockito |
| **Version Control** | Git, GitHub |

## System Modules

| Module | Responsibility |
| --- | --- |
| **auth** | Login, JWT tokens, roles, permissions |
| **child** | Child profiles and information |
| **guardian** | Parent/guardian profiles and child associations |
| **enrollment** | Enrollment periods and status |
| **attendance** | Child check-in/out, staff clock-in/out, attendance history |
| **staff** | Staff profiles, employment information |
| **document** | Upload, store, and retrieve documents |
| **notification** | Parent alerts (check-in, check-out, incidents) |

Cross-cutting concerns that don't belong to a single business module live in their own shared packages: **config**, **exception**, **security**, and **common**.

## High-Level Architecture

```
[Admin / Staff / Parent]
        │
        ▼
 [React Frontend]
        │
        ▼
 [Spring Boot Application]
 ┌───────────────────────────────┐
 │  auth                         │
 │  child                        │
 │  guardian                     │
 │  enrollment                   │
 │  attendance                   │
 │  staff                        │
 │  document                     │
 │  notification                 │
 └───────────────────────────────┘
        │
        ▼
   [PostgreSQL]
```

Document files use local storage in Phase 1 and move to a dedicated file-storage solution (AWS S3) in Phase 3.

## Backend Layer Structure

Each module generally follows the same layered structure:

```
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

Additional components within a module may include DTOs, Entities, Validation, Exception handling, Security configuration, and Mappers.

## Internal Package Structure

```
com.kidbase/
├── auth/
│     ├── controller/
│     ├── service/
│     ├── repository/
│     └── model/
├── child/
│     ├── controller/
│     ├── service/
│     ├── repository/
│     └── model/
├── guardian/
│     ├── controller/
│     ├── service/
│     ├── repository/
│     └── model/
├── enrollment/
│     ├── controller/
│     ├── service/
│     ├── repository/
│     └── model/
├── attendance/
│     ├── controller/
│     ├── service/
│     ├── repository/
│     └── model/
├── staff/
│     ├── controller/
│     ├── service/
│     ├── repository/
│     └── model/
├── document/
│     ├── controller/
│     ├── service/
│     ├── repository/
│     └── model/
├── notification/
│     ├── service/
│     └── model/
├── config/
├── exception/
├── security/
└── common/
```

See [ADR-011](adr/0011-feature-based-package-organization.md) for the reasoning behind this feature-based organization.

## How Modules Communicate

All modules live inside one application and communicate through direct internal service calls:

- **attendance** → calls → **notification** (a child check-in triggers a parent notification)
- **security** → used by → every module (validates the JWT token and role on every request)
- **document** → stores files locally in Phase 1, moves to AWS S3 in Phase 3

## Security

- JWT tokens for authentication
- Role-based access control on every endpoint
- Passwords encrypted with BCrypt
- Input validation on all API endpoints
- HTTPS enforced in production (Phase 3)

## Development Phases

### Phase 1 — Core Application

- Build and validate all modules locally with Spring Boot and PostgreSQL
- No cloud, Docker, or CI/CD yet

### Phase 2 — Containerization

- Containerize the Spring Boot application
- Containerize PostgreSQL
- Run everything with docker-compose

### Phase 3 — Cloud & Storage

- Deploy the application to AWS EC2
- Move the database to AWS RDS (PostgreSQL)
- Connect AWS S3 for document storage
- Connect AWS SNS for email and SMS notifications
- Manage secrets with AWS Secrets Manager

### Phase 4 — CI/CD

- Set up a GitHub Actions pipeline
- Automate testing on every push
- Automate deployment to AWS on every merge to main

### Phase 5 — Future (Microservices)

- Extract modules into independent services as the system scales
- Add Kafka for event-driven communication
- Add Kubernetes for container orchestration

## Architectural Goal

The goal is to keep KidBase simple enough to develop and maintain as a student project while still demonstrating professional software engineering practices: separation of concerns, maintainability, testing, and scalability.
