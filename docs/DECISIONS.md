# Racoon Store — Technical Decision Log

**Status:** Active  
**Version:** 0.1  
**Last updated:** 2026-11-16

## 1. Purpose

This document records the main architectural, technical and operational decisions made during the development of Racoon Store.

Its purpose is to:

- Preserve the reasoning behind important decisions.
- Maintain consistency throughout development.
- Help developers and AI agents understand the project.
- Avoid repeatedly discussing previously resolved choices.
- Support informed changes when requirements evolve.

Each decision has a unique identifier.

### Decision Statuses

- **Accepted:** Approved and currently applicable.
- **Proposed:** Under consideration.
- **Superseded:** Replaced by a newer decision.
- **Rejected:** Considered but not adopted.

Accepted decisions must be respected unless explicitly reconsidered.

---

## D-001 — Single Monorepo

**Status:** Accepted

**Decision:** Use a single Git repository with pnpm workspaces.

The repository will contain:

- Public storefront.
- Administration dashboard.
- Backend API.
- Shared packages.
- Project documentation.
- OpenCode instructions and skills.

**Rationale:**

All applications belong to the same business and will evolve together.

A monorepo simplifies version control, shared code management and development coordination.

**Consequences:**

Applications remain logically independent even though they share one repository.

A monorepo does not require deploying all applications to the same server.

---

## D-002 — Modular Monolith

**Status:** Accepted

**Decision:** Implement a modular monolithic backend using Node.js and Fastify.

Backend modules will be organized by business responsibility.

Examples:

- Authentication.
- Catalog.
- Inventory.
- Reservations.
- Orders.
- Payments.
- Shipping.

**Rationale:**

A single-store application does not justify the operational complexity of microservices.

Modular boundaries allow us to maintain, test and evolve features independently.

**Consequences:**

Modules must have clear responsibilities and controlled dependencies.

Business logic must not be duplicated across unrelated modules.

We will not build a universal backend framework in advance.

---

## D-003 — TypeScript

**Status:** Accepted

**Decision:** Use TypeScript across the frontend, backend and shared packages.

**Rationale:**

TypeScript improves consistency between application boundaries and helps detect errors during development.

**Consequences:**

- Use strict TypeScript configuration.
- Avoid unjustified `any`.
- Define explicit public contracts.
- Maintain compatibility between shared packages.
- Validate external input at runtime, even when TypeScript types exist.

---

## D-004 — Astro + React Storefront

**Status:** Accepted

**Decision:** Use Astro for the public storefront with React for interactive functionality.

**Rationale:**

The storefront will have product catalogs, product detail pages and other content that benefits from efficient rendering and search engine indexing.

React will handle features requiring browser interaction.

**Consequences:**

- Prefer Astro components for static presentation and layouts.
- Use React islands when interactivity is required.
- Avoid unnecessary hydration.
- Keep rendering and browser-state responsibilities clear.

The exact rendering and deployment configuration remains open.

---

## D-005 — React + Vite Administration

**Status:** Accepted

**Decision:** Develop the private administration dashboard with React and Vite.

**Rationale:**

The dashboard will contain interactive forms, data tables, inventory management and operational workflows.

React is appropriate for this application.

**Consequences:**

The dashboard will be maintained as a separate application inside the monorepo.

Both the dashboard and storefront will consume the same backend API.

---

## D-006 — PostgreSQL and Supabase

**Status:** Accepted

**Decision:** Use PostgreSQL as the relational database, initially hosted on Supabase.

**Rationale:**

The project requires relational integrity, transactions and reliable concurrent inventory operations.

Supabase provides managed PostgreSQL infrastructure.

**Consequences:**

- PostgreSQL is the system of record.
- Business logic remains in our Fastify backend.
- The application should not depend unnecessarily on Supabase-specific database functionality.
- Database credentials must remain server-side.
- Database portability should be preserved where practical.

Supabase Storage is the initial choice for product images.

---

## D-007 — Drizzle ORM

**Status:** Accepted

**Decision:** Use Drizzle for database access, schema definitions and migrations.

**Rationale:**

Drizzle provides TypeScript integration and explicit SQL-oriented database management.

**Consequences:**

- Schema changes require controlled migrations.
- Database constraints must be maintained explicitly.
- Applied migrations must not be modified casually.
- Critical operations must use appropriate database transactions.

---

## D-008 — Custom UI Component System

**Status:** Accepted

**Decision:** Build our own small UI component system using Tailwind CSS and custom components.

Do not adopt shadcn/ui or a complete third-party component system.

**Rationale:**

We want direct control over component implementation, design and behavior.

Developing these components manually is also part of the learning process.

**Consequences:**

- Components are created incrementally.
- Reusable primitives remain independent of business logic.
- Application-specific components stay near their features.
- The developer will implement core UI components personally.
- OpenCode may review or assist but must not generate components without an explicit request.
- Shared components are extracted only when justified.

---

## D-009 — Shared Packages Without Premature Abstraction

**Status:** Accepted

**Decision:** Establish clear package boundaries for database infrastructure, shared contracts and reusable UI.

**Rationale:**

Sharing appropriate code reduces duplication, but extracting everything prematurely increases complexity.

**Consequences:**

`packages/shared` contains safe cross-application contracts.

`packages/database` contains server-side database infrastructure.

`packages/ui` is reserved for genuinely shared UI components.

Astro-specific components remain in the storefront unless a clear reason to share them exists.

Shared packages should not become collections of unrelated utilities.

---

## D-010 — Mercado Pago Checkout Pro

**Status:** Accepted

**Decision:** Use Mercado Pago Checkout Pro for automatic online payments.

**Rationale:**

Mercado Pago supports online checkout in Uruguay and does not require a fixed monthly API subscription under its standard integration model.

Its transaction commissions will be considered when defining product prices.

**Consequences:**

- Checkout integration belongs in the backend.
- Payment notifications must be verified.
- Browser redirects are not proof of payment.
- Duplicate notifications must be handled safely.
- Production credentials remain private.
- The integration must follow the provider's current requirements.

Additional payment providers are not part of the initial release.

---

## D-011 — Manually Coordinated Bank Transfers

**Status:** Accepted

**Decision:** Allow customers to coordinate bank transfers directly with the store through WhatsApp.

**Rationale:**

This provides a payment alternative without implementing bank reconciliation integrations.

**Consequences:**

- The administrator reserves the product before providing payment instructions.
- The customer transfers directly to the provided account.
- The administrator verifies receipt of funds.
- The payment is confirmed manually in the dashboard.
- A transfer receipt alone is not sufficient proof.
- The system must support late-payment exceptions.

Automated bank transfer verification is outside the initial scope.

---

## D-012 — Replaceable Payment Integrations

**Status:** Accepted

**Decision:** Keep payment provider-specific code behind a clear integration boundary.

**Rationale:**

We may introduce Bamboo or another provider in the future.

Orders and inventory should not depend directly on Mercado Pago-specific data structures.

**Consequences:**

- Payment records use internal domain concepts.
- Provider-specific requests and responses stay inside their integrations.
- New providers can be added through focused changes.
- Do not implement speculative payment adapters or a complex plugin framework.

---

## D-013 — Centralized Inventory and Reservations

**Status:** Accepted

**Decision:** Use the backend and PostgreSQL as the authority for stock and reservations.

**Rationale:**

The business sells limited-stock products across multiple channels.

Inventory must remain consistent between website purchases and manually coordinated sales.

**Consequences:**

- Reservations reduce available inventory.
- Manual reservations last 10 minutes by default.
- A 5-minute option is available.
- Administrators can extend or cancel reservations.
- Expiration is enforced server-side.
- Concurrent reservation operations must be safe.
- Confirmed sales must not release reserved stock.
- Late payments require explicit reconciliation.

Online payment reservations may use different expiration rules.

---

## D-014 — New and Used Product Models

**Status:** Accepted

**Decision:** Handle new and used products differently.

**New products:**

A model can contain multiple size variants with separate stock quantities.

**Used products:**

Each individual item has its own listing, images, price, condition and one initial stock unit.

**Rationale:**

Used items may differ significantly in condition and measurements, even when they share a model.

New interchangeable items can be grouped more efficiently.

**Consequences:**

The catalog and inventory models must support both cases without duplicating unnecessary business logic.

---

## D-015 — Delivery and Pickup

**Status:** Accepted

**Decision:** Support free pickup by arrangement and paid delivery options.

**Rationale:**

Customers need flexibility, and shipping should not be assumed to be free.

**Consequences:**

- Customers choose a delivery method during checkout.
- Applicable delivery costs are included in the final total.
- Administrators manage delivery options and prices.
- Pickup has no shipping fee.
- Courier APIs are not required initially.

Exact delivery rates and available transport providers remain undecided.

---

## D-016 — Guest Checkout

**Status:** Accepted

**Decision:** Allow customers to purchase without creating an account.

**Rationale:**

Mandatory registration adds friction to a small store with occasional buyers.

**Consequences:**

- Checkout collects only the information required to complete the purchase and delivery.
- Customer accounts are not required initially.
- Administrative authentication remains mandatory.
- Order and customer data require appropriate privacy protections.

---

## D-017 — Incremental Development With OpenCode

**Status:** Accepted

**Decision:** Use OpenCode for focused implementation tasks, following repository documentation and project-specific skills.

**Rationale:**

Small tasks are easier to understand, review, test and maintain.

The developer also wants to understand the implementation rather than delegate the entire project without supervision.

**Consequences:**

- Tasks have limited scope.
- Relevant documentation is consulted.
- Existing patterns are respected.
- Changes are verified before progressing.
- Unrelated refactors are avoided.
- OpenCode does not commit or push without explicit approval.
- Documentation must reflect implemented behavior.

---

## D-018 — Single Store, Single Administrator Initially

**Status:** Accepted

**Decision:** Build the first release for one store and one administrative account.

**Rationale:**

Multiple businesses, branches and staff roles are not currently required.

**Consequences:**

- Business operations are scoped to Racoon Store.
- Administrative access must be authenticated.
- Customer accounts are optional future functionality.
- Multi-tenant architecture is not required.
- Additional administrative permissions can be evaluated later.

---

## 2. Pending Decisions

The following decisions remain open.

### P-001 — Hosting and Deployment

Select hosting for:

- Astro storefront.
- React dashboard.
- Fastify backend.

Evaluate the existing Hostinger plan and alternative providers.

### P-002 — Authentication Implementation

Choose the concrete authentication and session strategy for the administration dashboard.

### P-003 — Final Visual Identity

Define:

- Brand colors.
- Typography.
- Design tokens.
- Light/dark theme requirements.
- Storefront layout.
- Dashboard visual conventions.

### P-004 — Shipping Tariffs

Define:

- Local delivery zones.
- Domestic shipping arrangements.
- Pickup procedures.
- Delivery fees.
- Carrier selection.

### P-005 — Online Payment Reservation Policy

Define exact expiration and reconciliation rules for Mercado Pago checkout attempts, including delayed confirmations.

### P-006 — Returns and Exchanges

Define store policies for:

- New products.
- Used products.
- Cancellations.
- Refunds.
- Returns and exchanges.

These policies must be reviewed before production.

### P-007 — Detailed Database Model

Finalize database entities, relationships, constraints and inventory accounting rules before creating production migrations.

### P-008 — Notifications

Determine whether customers and administrators require email or other automated notifications.

---

## 3. Changing Decisions

When changing an accepted decision:

1. Identify the existing decision.
2. Explain why reconsideration is necessary.
3. Evaluate the relevant alternatives.
4. Record the new decision and its consequences.
5. Mark the previous decision as superseded when applicable.
6. Update related project documentation.
7. Implement changes only after approval.

Avoid silently changing foundational architectural decisions during unrelated tasks.

## 4. Documentation Consistency

This document records architectural and operational decisions.

Detailed implementation rules belong in the relevant documentation:

- `PROJECT.md`: scope and functionality.
- `ARCHITECTURE.md`: technical organization.
- `BUSINESS_RULES.md`: business behavior.
- `UI_GUIDELINES.md`: interface conventions.
- `ROADMAP.md`: implementation sequence.

If documents disagree, identify and resolve the inconsistency before implementing the affected functionality.

Do not assume this decision log overrides newer, explicitly approved changes.
