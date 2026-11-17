
# Racoon Store - Agent Instructions

## 1. Project Overview

Racoon Store is an online store for clothing, footwear, and accessories.

Tech stack:
- Monorepo: pnpm workspaces
- Language: TypeScript
- Storefront: Astro + React
- Admin dashboard: React + Vite
- API: Node.js + Fastify
- Database: PostgreSQL (Supabase)
- ORM: Drizzle
- UI: Tailwind CSS + custom-built components
- Payments: Mercado Pago and manually confirmed bank transfers

Architecture: modular monolith.

## 2. Repository Structure

Target structure:

- apps/web: public storefront
- apps/admin: administration dashboard
- apps/api: backend and business logic
- packages/database: schema, queries and migrations
- packages/ui: reusable UI primitives
- packages/shared: shared types and contracts
- docs: project and technical documentation

Create directories only when needed by the current task.

## 3. Development Principles

- Use strict TypeScript. Avoid `any` unless justified.
- Write readable, maintainable and cohesive code.
- Follow existing patterns before introducing new ones.
- Keep modules focused on clear responsibilities.
- Prefer simple solutions over unnecessary abstractions.
- Extract reusable code when duplication or a clear shared responsibility exists.
- Avoid premature generalization and overengineering.
- Remove dead code only when its removal is safe and within scope.
- Do not introduce dependencies without a clear reason.
- Preserve existing behavior during refactors.

## 4. Architecture Rules

- Keep business logic in the backend.
- Separate HTTP handlers, validation, services and data access.
- Avoid large files containing unrelated responsibilities.
- Keep application-specific logic inside its application.
- Share code through packages only when appropriate.
- Do not create circular dependencies.
- Do not build a universal backend or microservices.
- Keep payment providers behind a replaceable integration boundary.
- Do not make frontend applications responsible for authoritative inventory or payment state.

## 5. UI Development

- Use the shared UI system for reusable primitives.
- Prefer existing components before creating new ones.
- Use Tailwind CSS and the established design tokens.
- Build UI components with Tailwind CSS and custom-built components.
- Keep visual styling consistent between applications.
- Keep business-specific components near their features.
- Design responsive and accessible interfaces.
- Support keyboard interaction and semantic HTML.
- Avoid duplicated styling and unnecessary one-off components.

## 6. Database and API

- Use Drizzle for database access and migrations.
- Validate external input on the server.
- Keep schema changes synchronized with migrations.
- Use database transactions when consistency is required.
- Enforce inventory consistency at the database level.
- Never rely only on frontend checks to prevent overselling.
- Do not modify existing migrations that have already been applied.
- Avoid breaking API contracts without explicit approval.

## 7. Payments and Inventory

- Mercado Pago handles automatic online payments.
- Bank transfers are confirmed manually by an administrator.
- Never trust client-side payment success alone.
- Verify payment notifications before confirming orders.
- Make payment processing idempotent.
- Reserve stock before completing checkout.
- Support manual reservations with configurable expiration.
- Default manual reservation duration: 10 minutes.
- Allow a 5-minute reservation option.
- Release expired unpaid reservations safely.
- Handle late transfers through manual reconciliation.
- Prevent concurrent reservations from overselling stock.

## 8. Security

- Never expose secrets or credentials in client code.
- Never commit .env files or sensitive information.
- Enforce authentication and authorization on the backend.
- Validate and sanitize untrusted input as appropriate.
- Use least-privilege access where possible.
- Do not bypass security controls to simplify development.

## 9. Agent Workflow

- Inspect only the files relevant to the requested task.
- Keep changes small, focused and incremental.
- Do not implement unrelated features.
- Do not perform unrelated refactors.
- Avoid unnecessary full-project scans.
- Reuse established code and project conventions.
- Ask before making major architectural changes.
- Do not delete important files or data without approval.
- Do not commit or push unless explicitly requested.
- Use focused checks after changes.
- Avoid unnecessary long-running builds.

After each task, report:
1. What changed.
2. What was verified and the results.
3. Any remaining issues or risks.

Never claim tests passed unless they were executed.

## 10. Documentation

- Keep documentation aligned with implemented behavior.
- Read relevant docs before changing their associated modules.
- Do not load all documentation unnecessarily.
- Update architecture or decision records when relevant.
- Keep AGENTS.md concise and repository-wide.
- Put detailed rules in docs or specialized skills.
