<div align="center">

# KidBase

**A preschool and daycare management system, designed and documented using a full software development lifecycle.**

[![Java](https://img.shields.io/badge/Java-21-ED8B00?logo=openjdk&logoColor=white)](https://openjdk.org/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-4.1.1-6DB33F?logo=springboot&logoColor=white)](https://spring.io/projects/spring-boot)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Maven](https://img.shields.io/badge/Maven-C71A36?logo=apachemaven&logoColor=white)](https://maven.apache.org/)
[![Status](https://img.shields.io/badge/status-in%20development-yellow)]()

</div>

---

## What is KidBase?

KidBase is a preschool and daycare management system dedicated to automating the daily, repetitive, and time-consuming tasks that burden childcare businesses — such as paperwork, attendance tracking, and document management — making business operations smoother, faster, and more efficient.

## Engineering highlights

- **Full SDLC documentation** — vision, stakeholders, requirements, user stories, MoSCoW prioritization, backlog, MVP scope, architecture, domain model, database design, and API design are captured in [`docs/`](docs/) rather than left implicit in the code.
- **11 Architecture Decision Records** — each significant technical decision (modular monolith, soft deletes, JWT authentication, feature-based packaging, and others) is recorded with context, reasoning, and trade-offs in [`docs/adr/`](docs/adr/).
- **Diagrams as documentation** — C4 context and container diagrams, a conceptual data model, a full ERD, and user-flow diagrams accompany the written design (see below).
- **Security decisions made explicit** — the API design specifies that public registration cannot create an ADMIN or STAFF account, and that identity for sensitive actions (check-in/check-out) is derived from the JWT rather than trusted from client input. See [ADR-005](docs/adr/0005-public-registration-parent-role.md) and [ADR-009](docs/adr/0009-staff-identity-from-jwt.md).
- **Phased delivery plan** — the core application is built and validated first; containerization, cloud deployment, and CI/CD are scoped as later, deliberate phases rather than added upfront. See [ADR-010](docs/adr/0010-build-in-phases.md).

## Architecture at a glance

**C4 Context** — who uses KidBase and what it depends on:

![C4 Context Diagram](docs/diagrams/01-c4-context.svg)

**C4 Container** — the modular monolith: one Spring Boot application, eight feature modules, one database:

![C4 Container Diagram](docs/diagrams/02-c4-container.svg)

## Data model

**Conceptual model** — the domain at a glance:

![Conceptual Data Model](docs/diagrams/03-conceptual-data-model.svg)

**Full ERD** — 11 PostgreSQL tables with keys and constraints:

![Database ERD](docs/diagrams/04-database-erd.svg)

## User flow

What each role does, step by step, in the MVP:

![User Flow Diagram](docs/diagrams/05-user-flow.svg)

## Tech stack

| Layer | Technology | Status |
| --- | --- | --- |
| Backend | Java 21, Spring Boot 4, Maven | Implemented |
| Database | PostgreSQL | Implemented |
| Persistence | Spring Data JPA, Hibernate | Implemented |
| Version Control | Git, GitHub | Implemented |
| Frontend | React | Planned |
| Security | Spring Security, JWT | Planned |
| Testing | JUnit, Mockito | Planned |
| API Docs | Swagger / OpenAPI | Planned |

The repository currently contains a working Spring Boot and PostgreSQL backend skeleton with the module layout described below. Authentication, security, the React frontend, and automated tests are fully specified in [`docs/`](docs/) but not yet implemented.

## Project structure

KidBase is organized as a **modular monolith** — a single deployable Spring Boot application, internally split into self-contained feature modules (see [ADR-011](docs/adr/0011-feature-based-package-organization.md)):

```
com.kidbase
├── auth            # login, JWT issuing/validation, roles
├── child           # child profiles
├── attendance      # child check-in/out, staff clock-in/out
├── staff           # staff profiles & employment info
├── guardian        # parent/guardian profiles & child links
├── enrollment      # enrollment periods & status
├── document        # upload/store/retrieve documents
├── notification    # parent alerts (check-in, check-out, incidents)
├── config          # cross-cutting configuration
├── exception       # centralized exception handling
├── security        # JWT filters, role-based access control
├── common          # shared utilities
└── KidBaseApplication
```

## MVP scope

**In scope:** authentication and role-based access, child enrollment, digital attendance check-in/check-out, staff management, and automatic parent notifications on check-in/check-out.

**Deliberately out of scope for v1:** billing and payments, infant monitoring logs, daily menus and activity scheduling, staff scheduling, analytics, and a full parent portal. See [`docs/08-mvp-definition.md`](docs/08-mvp-definition.md) for the full reasoning.

## Documentation

| # | Document |
| --- | --- |
| 01 | [Project Vision](docs/01-project-vision.md) |
| 02 | [Problem Discovery](docs/02-problem-discovery.md) |
| 03 | [Stakeholders](docs/03-stakeholders.md) |
| 04 | [Requirements](docs/04-requirements.md) |
| 05 | [User Stories](docs/05-user-stories.md) |
| 06 | [MoSCoW Prioritization](docs/06-moscow-prioritization.md) |
| 07 | [Product Backlog](docs/07-product-backlog.md) |
| 08 | [MVP Definition](docs/08-mvp-definition.md) |
| 09 | [System Design](docs/09-system-design.md) |
| 10 | [Architecture Design](docs/10-architecture-design.md) |
| 11 | [Domain Model](docs/11-domain-model.md) |
| 12 | [Database Design](docs/12-database-design.md) |
| 13 | [API Design](docs/13-api-design.md) |
| — | [Architecture Decision Records](docs/adr/) |
| — | [Diagrams](docs/diagrams/) |

## Getting started

**Prerequisites:** Java 21, Maven (or the included wrapper), and PostgreSQL running locally on **port 5433** (see `spring.datasource.url` in `application.properties` — this project does not use PostgreSQL's default port 5432).

**macOS / Linux (bash):**

```bash
# 1. Create the database (adjust -p if your local Postgres uses a different port)
createdb -p 5433 kidbase

# 2. Set your local PostgreSQL credentials as environment variables
export DB_USERNAME=postgres
export DB_PASSWORD=your_local_password

# 3. Run the application
./mvnw spring-boot:run
```

**Windows (PowerShell):**

```powershell
# 1. Create the database (adjust -p if your local Postgres uses a different port)
createdb -p 5433 kidbase

# 2. Set your local PostgreSQL credentials as environment variables
$env:DB_USERNAME = "postgres"
$env:DB_PASSWORD = "your_local_password"

# 3. Run the application
.\mvnw.cmd spring-boot:run
```

The API is available at `http://localhost:8081`.

> Database credentials are read from the `DB_USERNAME` / `DB_PASSWORD` environment variables in `src/main/resources/application.properties`. No credentials are committed to the repository.

## Roadmap

| Phase | Focus |
| --- | --- |
| 1 — Core Application | Spring Boot + PostgreSQL, all modules working locally |
| 2 — Containerization | Docker + docker-compose |
| 3 — Cloud & Storage | AWS EC2/RDS, S3 for documents, SNS for notifications |
| 4 — CI/CD | GitHub Actions for automated testing and deployment |
| 5 — Future | Evaluate microservices, Kafka, and Kubernetes if real scale requirements justify them |

See [ADR-010](docs/adr/0010-build-in-phases.md) for the reasoning behind this phased approach.
