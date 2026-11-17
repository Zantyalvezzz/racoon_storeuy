
---
name: backend-module
description: Implement, extend, test, or refactor Fastify backend modules in Racoon Store. Use for API routes, services, validation, repositories, authentication, and business workflows.
---

# Backend Module Development

## Context

Follow:
- AGENTS.md
- docs/ARCHITECTURE.md
- docs/BUSINESS_RULES.md

Read only the documentation relevant to the task.

Stack: TypeScript, Node.js, Fastify, Drizzle, PostgreSQL.

Architecture: modular monolith.

## Workflow

1. Identify the requested module and behavior.
2. Inspect its existing implementation and dependencies.
3. Identify the smallest required change.
4. Define inputs, outputs, and business invariants.
5. Implement within the established architecture.
6. Add focused tests when appropriate.
7. Run relevant verification.
8. Summarize changes and remaining risks.

Do not implement unrelated features.

## Module Responsibilities

Organize code by feature.

Use these boundaries when justified:

- Routes: HTTP handling and endpoint registration.
- Schemas: external input and response validation.
- Services: business rules and workflows.
- Repositories: persistence operations.
- Integrations: external providers and infrastructure.

Do not create empty layers or unnecessary abstractions.

Keep HTTP-specific concerns out of domain logic.

## API Conventions

- Use versioned API routes.
- Validate untrusted input.
- Return consistent error responses.
- Enforce authentication and authorization server-side.
- Keep API contracts stable.
- Avoid exposing database implementation details.

## Business Integrity

- Treat PostgreSQL as the source of truth.
- Use transactions for consistency-sensitive operations.
- Never trust client-supplied prices or stock values.
- Prevent concurrent inventory overselling.
- Make retried financial operations idempotent.
- Keep order, payment, and fulfillment states separate.
- Verify external payment notifications.

Follow BUSINESS_RULES.md for detailed domain behavior.

## Dependencies

- Reuse existing infrastructure and established patterns.
- Avoid circular module dependencies.
- Prefer explicit module boundaries.
- Do not introduce packages without justification.
- Do not build a generic backend framework.

## Refactoring

- Preserve existing behavior unless change is requested.
- Avoid unrelated cleanup.
- Remove confirmed dead code only within scope.
- Keep public contracts compatible unless approved otherwise.
- Add or update tests for changed behavior.

## Verification

Prefer focused checks:

- Typechecking.
- Relevant unit tests.
- Relevant integration tests.
- Linting for changed modules.

Run broader checks only when justified.

Never claim verification passed without executing it.

## Reporting

Report:
1. Files changed.
2. Behavior implemented.
3. Tests and checks executed.
4. Remaining issues or risks.

Do not commit or push without approval.
