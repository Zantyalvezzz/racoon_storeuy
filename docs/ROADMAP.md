# Racoon Store — Development Roadmap

**Status:** Planning  
**Version:** 0.2  
**Scope:** Ordered delivery plan for the initial single-store release.

This roadmap tracks implementation sequence and completion gates. Product, inventory, reservation, order, payment, shipping, and fulfillment policies remain defined in `BUSINESS_RULES.md`; this document does not restate them. Resolve pending decisions in `DECISIONS.md` before implementing the affected behavior. Tasks may be refined as implementation evidence becomes available, without silently changing accepted decisions.

## Phase 0 — Documentation and Agent Skills

**Depends on:** None  
**Status:** Complete

- [x] Use the canonical Racoon Store brand in project documentation and skills.
- [x] Use the canonical `AGENTS.md` and `docs/PROJECT.md` filenames and update the architecture/documentation references.
- [x] Keep agent instructions and skills aligned with the accepted architecture and custom UI decisions.
- [x] Keep business policies in `BUSINESS_RULES.md`, technical decisions in `DECISIONS.md`, and this file focused on delivery sequence.

**Completion criteria:** Documentation links resolve, agent guidance is consistent with accepted decisions, and the roadmap does not duplicate business rules.

## Phase 1 — Monorepo Setup

**Depends on:** Phase 0

- [ ] Initialize pnpm workspace configuration and shared repository scripts.
- [ ] Establish strict TypeScript, formatting, linting, and test conventions.
- [ ] Create application and package scaffolding following `ARCHITECTURE.md` (`apps/web`, `apps/admin`, `apps/api`, and the required `packages/*`).
- [ ] Define package boundaries and workspace dependency rules; avoid speculative packages or abstractions.
- [ ] Add safe environment-variable examples and ensure secrets and local environment files are excluded from version control.
- [ ] Document local setup and focused verification commands.

**Completion criteria:** A clean install recognizes the intended workspaces, baseline checks run, and each application/package has a clear purpose and documented local setup.

## Phase 2 — Database and API Foundations

**Depends on:** Phase 1  
**Decision gates:** Finalize the detailed data model (DECISIONS.md P-007). Select the admin authentication/session strategy before implementing protected admin access (P-002).

- [ ] Establish the PostgreSQL connection and Drizzle schema/migration workflow.
- [ ] Define initial entities, relationships, constraints, and migration conventions based on `ARCHITECTURE.md` and `BUSINESS_RULES.md`.
- [ ] Create the Fastify application, versioned `/api/v1` routes, request validation, consistent errors, and health endpoint.
- [ ] Establish shared API contracts without exposing database internals or server-only code.
- [ ] Add authorization foundations and validate that protected operations are enforced by the backend.
- [ ] Add focused database/API tests and document migration safety procedures.

**Completion criteria:** A local or test database can be migrated reproducibly; the API starts, validates requests, exposes its health endpoint, and has verified database connectivity and protected-route foundations.

## Phase 3 — Catalog and Administration

**Depends on:** Phase 2

- [ ] Finalize and implement catalog entities and validation for the product cases described in `BUSINESS_RULES.md`.
- [ ] Implement backend catalog operations for creating, editing, publishing, and archiving listings and managing variants/categories.
- [ ] Add authorized admin workflows for catalog management.
- [ ] Establish product image upload/storage handling using the chosen storage boundary and server-authorized access.
- [ ] Implement public catalog and product-detail API contracts; expose only published catalog data as specified by the business rules.
- [ ] Add focused tests for catalog validation, visibility, and historical data boundaries.

**Completion criteria:** An authorized administrator can manage catalog records, and the public storefront can retrieve published listings and details through the API.

## Phase 4 — Inventory and Reservations

**Depends on:** Phases 2 and 3

- [ ] Implement inventory accounting and database concurrency controls in accordance with `BUSINESS_RULES.md` §§4–5 and 11.
- [ ] Implement audited administrative stock adjustments and externally coordinated sales.
- [ ] Implement manual reservation creation, extension, cancellation, and backend-enforced expiration.
- [ ] Add an expiration processor or transaction-time checks, with concurrency-safe handling.
- [ ] Add admin visibility for stock and reservation status.
- [ ] Implement focused tests for the critical inventory and reservation scenarios listed in `BUSINESS_RULES.md` §13.

**Completion criteria:** Concurrent stock operations preserve the documented invariants, reservation lifecycle actions are available to authorized administrators, and focused tests cover expiration and contention cases.

## Phase 5 — Orders and Checkout

**Depends on:** Phases 3 and 4  
**Decision gate:** Define the order and fulfillment state machines before implementing their transitions, as required by `BUSINESS_RULES.md`.

- [ ] Implement guest order creation and order-item snapshots using server-authoritative product data.
- [ ] Implement checkout validation for quantities, current prices, availability, customer/delivery details, and a server-generated total.
- [ ] Create pending orders and reserve required stock atomically; make retries safe and reject invalid transitions.
- [ ] Define a delivery-quote contract for checkout without encoding unconfirmed tariffs; complete its configured calculation in Phase 7.
- [ ] Add order lookup and administrative order-detail workflows with appropriate authorization.
- [ ] Add focused tests for order snapshots, totals, idempotency, and cancellation/stock interactions.

**Completion criteria:** The backend can create and manage a pending guest order from validated input without trusting client prices or stock; checkout has a defined delivery-quote boundary for Phase 7 integration.

## Phase 6 — Payments

**Depends on:** Phases 4 and 5  
**Decision gate:** Resolve the Mercado Pago reservation timeout and reconciliation policy (DECISIONS.md P-005) before implementing release behavior for pending payments.

- [ ] Implement provider-independent payment operations and keep Mercado Pago-specific structures inside its integration.
- [ ] Implement server-side Mercado Pago Checkout Pro preference creation and verified, idempotent payment notification handling.
- [ ] Implement the administrator workflow for manually coordinated bank-transfer confirmation and exception review.
- [ ] Coordinate payment outcomes with order and reservation transitions using the rules in `BUSINESS_RULES.md` §§6, 8, and 11–13.
- [ ] Add tests for verified notifications, duplicate delivery, failure/retry handling, manual confirmation, and late-payment exceptions.
- [ ] Keep end-to-end checkout activation gated on Phase 7's final server-generated delivery quote.

**Completion criteria:** Payment operations update internal payment/order state safely and idempotently, manual confirmation is protected, and late or duplicate events follow an explicit reviewed path.

## Phase 7 — Shipping and Fulfillment

**Depends on:** Phase 5; payment checkout wiring from Phase 6 remains gated until this phase is complete.  
**Decision gate:** Resolve delivery areas and tariffs (DECISIONS.md P-004).

- [ ] Implement administrator-managed delivery methods/settings and the server-side delivery quote used by checkout.
- [ ] Ensure the order records the confirmed delivery selection and amount used to calculate its payable total.
- [ ] Implement fulfillment state and admin workflows independently of payment state, following `BUSINESS_RULES.md` §9.
- [ ] Connect the final delivery quote and order total to checkout and payment initiation.
- [ ] Keep courier APIs and automated shipping labels out of scope unless separately approved.

**Completion criteria:** Checkout receives a complete server-calculated total before payment initiation, administrators can manage delivery and fulfillment operations, and payment and fulfillment states remain separate.

## Phase 8 — UI Completion

**Depends on:** Phases 3–7 for the corresponding workflows. UI work should also proceed incrementally within those phases.

- [ ] Finalize approved visual foundations and design tokens before treating brand styling as settled (DECISIONS.md P-003).
- [ ] Complete responsive storefront catalog, product detail, cart, checkout, delivery selection, and customer feedback states.
- [ ] Complete responsive admin workflows for catalog, inventory, reservations, orders, payments, and fulfillment.
- [ ] Use Astro for primarily static presentation and React where browser interaction is required; avoid unnecessary hydration.
- [ ] Apply the custom component and accessibility conventions in `UI_GUIDELINES.md`.
- [ ] Verify keyboard interaction, semantic markup, loading/empty/error/disabled states, and small-screen layouts.

**Completion criteria:** The storefront and admin support the implemented end-to-end workflows accessibly and responsively, using the approved visual system without duplicating authoritative business logic in the clients.

## Phase 9 — Testing and Deployment

**Depends on:** Phases 1–8  
**Decision gates:** Select hosting/deployment providers (DECISIONS.md P-001); close any production-blocking decisions, including authentication, payment timeouts, shipping, and notifications where applicable.

- [ ] Run focused unit and integration tests for catalog, inventory, reservations, orders, payments, authorization, and fulfillment.
- [ ] Verify concurrent last-unit attempts, duplicate/retried requests, payment notification idempotency, reservation expiration races, and end-to-end checkout.
- [ ] Run typechecking, linting, production builds, migration checks, and security review for all applications.
- [ ] Configure production environments, secrets, database migration procedure, storage access, HTTPS, and operational health checks.
- [ ] Deploy storefront, admin, API, database, and image storage to verified compatible targets; do not assume shared hosting supports persistent API processes.
- [ ] Verify production smoke flows and document backup, recovery, rollback, and operational procedures.

**Completion criteria:** The release passes documented functional and security checks, production services are reachable over secure endpoints, database changes are reproducible, and the administrator can complete the core operating flows in `PROJECT.md` without manual database edits.
