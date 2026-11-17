
---
name: database-migration
description: Design, generate, review, or validate PostgreSQL schema changes and Drizzle migrations for Racoon Store. Use for tables, relations, constraints, indexes, and database integrity changes.
---

# Database Migration

## Context

Follow:
- AGENTS.md
- docs/ARCHITECTURE.md
- docs/BUSINESS_RULES.md

Stack: PostgreSQL, Drizzle ORM, TypeScript.

Database package: packages/database.

## General Rules

- PostgreSQL is the source of truth.
- Treat schema changes as potentially destructive.
- Preserve existing data and integrity.
- Prefer explicit constraints where appropriate.
- Do not modify previously applied migrations.
- Do not apply migrations to shared or production databases without explicit approval.
- Never use destructive schema synchronization as a substitute for reviewed migrations.

## Workflow

1. Identify the requested data model change.
2. Inspect relevant existing schema and migrations.
3. Identify affected tables, constraints, and dependencies.
4. Consider compatibility with existing data.
5. Propose the smallest safe schema change.
6. Update Drizzle schema definitions.
7. Generate a migration when requested.
8. Inspect generated SQL.
9. Verify schema and migration consistency.
10. Report risks and verification results.

Do not modify unrelated tables.

## Data Modeling

- Define explicit primary and foreign keys.
- Add uniqueness constraints when required.
- Use check constraints for database invariants.
- Create indexes based on actual query needs.
- Use consistent naming conventions.
- Represent monetary amounts exactly.
- Handle timestamps consistently.
- Preserve historical order information.

Avoid speculative columns and premature generalization.

## Inventory Integrity

Database changes related to inventory must consider:

- Physical stock.
- Active reservations.
- Committed stock.
- Available stock.
- Concurrent operations.
- Reservation expiration.
- Confirmed sales.

Prevent negative available inventory and duplicate stock deductions.

Use transactions and appropriate concurrency controls.

Do not rely solely on application-level checks.

## Migration Safety

Before applying a migration:

- Review generated SQL.
- Check whether operations can lose data.
- Review new NOT NULL constraints.
- Check existing records for constraint violations.
- Consider table locks and migration compatibility.
- Confirm the target environment.
- Confirm rollback or recovery procedures when needed.

Never silently drop tables or columns.

Use a staged migration for incompatible changes when necessary.

## Verification

When an appropriate local or test database is available:

- Validate migration execution.
- Inspect database constraints.
- Verify expected schema changes.
- Run relevant database tests.
- Check for schema and migration drift.

If database access is unavailable, report the limitation.

Do not claim that a migration was applied or tested unless it actually was.

## Reporting

Report:
1. Schema files changed.
2. Migration files generated.
3. Database objects affected.
4. Verification results.
5. Data-loss or compatibility risks.

Do not commit or push without approval.
