# ADR-007 — Separate `users` Table from Domain Profile Tables

**Date:** 2026-09-12
**Status:** Accepted

## Context

KidBase has different types of users — admins, staff, and parents — each with different domain-specific information beyond basic authentication data.

## Decision

Separate the `users` table (authentication and system access) from domain profile tables (`staff`, `parent_guardians`) with a one-to-one relationship.

## Reasons

- Authentication concerns are decoupled from business domain concerns
- A guardian can exist in the system before receiving a login account
- Each domain profile can evolve independently
- Cleaner separation of responsibilities

## Trade-offs

- Requires a JOIN to get full user information
- Slightly more complex queries for user profiles
