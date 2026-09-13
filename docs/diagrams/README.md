# KidBase — Diagrams

This folder holds the visual diagrams that support the written docs. Each PNG has a matching SVG source.

| File | Purpose | Related doc |
| --- | --- | --- |
| [01-c4-context](01-c4-context.svg) | C4 Level 1 — who uses KidBase and which external systems it depends on | [09-system-design.md](../09-system-design.md) |
| [02-c4-container](02-c4-container.svg) | C4 Level 2 — the React frontend, Spring Boot modules, and the systems they talk to | [10-architecture-design.md](../10-architecture-design.md) |
| [03-conceptual-data-model](03-conceptual-data-model.svg) | The domain at a glance — main entities and how they relate | [11-domain-model.md](../11-domain-model.md) |
| [04-database-erd](04-database-erd.svg) | Full ERD — all 11 tables with columns, keys, and constraints | [12-database-design.md](../12-database-design.md) |
| [05-user-flow](05-user-flow.svg) | Step-by-step flow for Admin, Staff, and Parent in the MVP | [05-user-stories.md](../05-user-stories.md), [08-mvp-definition.md](../08-mvp-definition.md) |

## Diagram types used

| Diagram | Purpose |
| --- | --- |
| C4 Context | Who interacts with KidBase |
| C4 Container | Main technical parts of KidBase |
| Conceptual Data Model | Main business entities |
| ERD | Exact database structure |
| User Flow | What each user does |

Text-as-code sources (DBML for the ERD, Structurizr DSL for the C4 diagrams, Mermaid/PlantUML for anything else) aren't checked in yet — the SVGs above are the current source of truth. If those text sources get added later, keep them in this folder next to the rendered output they generate.
