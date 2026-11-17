# Racoon Store — Architecture

**Status:** Planning  
**Version:** 0.1  
**Architecture:** Modular Monolith  
**Repository:** pnpm Monorepo

## 1. Architecture Goals

Racoon Store will use a modular monolith organized in a single repository.

The architecture must prioritize:

- Clear separation of responsibilities.
- Maintainability and readability.
- Incremental development and verification.
- Reliable inventory and payment operations.
- Reusable code without premature abstraction.
- A custom-built UI component system.
- The ability to replace external integrations.
- Simplicity appropriate for a single-store business.

Do not introduce microservices, a universal backend or unnecessary infrastructure.

## 2. Technology Stack

| Layer | Technology |
|---|---|
| Monorepo | pnpm workspaces |
| Language | TypeScript |
| Storefront | Astro + React |
| Administration | React + Vite |
| API | Node.js + Fastify |
| Database | PostgreSQL |
| Database hosting | Supabase |
| ORM and migrations | Drizzle |
| Product images | Supabase Storage |
| Styling | Tailwind CSS |
| UI components | Custom-built |
| Online payments | Mercado Pago Checkout Pro |
| Manual payments | Bank transfer |
| Version control | Git + GitHub |

Deployment infrastructure will be selected separately after evaluating hosting requirements.

## 3. Repository Structure

```text
racoon-store/
├── apps/
│   ├── web/
│   ├── admin/
│   └── api/
│
├── packages/
│   ├── database/
│   ├── shared/
│   └── ui/
│
├── docs/
│   ├── PROJECT.md
│   ├── ARCHITECTURE.md
│   ├── BUSINESS_RULES.md
│   ├── UI_GUIDELINES.md
│   ├── ROADMAP.md
│   └── DECISIONS.md
│
├── .opencode/
│   └── skills/
│
├── AGENTS.md
├── package.json
├── pnpm-workspace.yaml
└── README.md
```

This is the target structure, not a requirement to generate every directory immediately.

Each application and package must have a clear purpose.

## 4. Application Responsibilities

### apps/web — Public Storefront

Technology: Astro + React.

Responsible for:

- Public pages and SEO.
- Product catalog and product details.
- Search, filters and navigation.
- Shopping cart.
- Checkout interface.
- Contact and delivery selection.
- Responsive storefront layout.

Astro should handle primarily static or server-rendered content.

React should be used for interactive features that need client-side state.

Do not introduce React hydration when it is unnecessary.

The storefront consumes backend APIs. It does not directly manage authoritative inventory, orders or payments.

Product availability must be validated again on the server during checkout.

The rendering and deployment strategy will be confirmed according to hosting requirements.

### apps/admin — Administration Dashboard

Technology: React + Vite.

Responsible for:

- Administrative authentication interface.
- Catalog management.
- Inventory and reservations.
- Orders and payments.
- Delivery management.
- Store settings.
- Basic reporting.

The dashboard interacts with the same backend API as the storefront.

It must not implement authoritative business rules in the browser.

Administrative operations require backend authentication and authorization.

### apps/api — Backend

Technology: Node.js + Fastify.

The backend owns:

- Business logic.
- Input validation.
- Authentication and authorization.
- Inventory consistency.
- Reservation expiration.
- Order creation and state transitions.
- Payment initiation and verification.
- Shipping calculations.
- Database access.
- External service integrations.

The API will use versioned routes, initially `/api/v1`.

HTTP handlers should remain thin. Business rules belong in services or domain-specific modules.

## 5. Backend Module Organization

Organize the API primarily by business feature.

Target modules:

- `auth`
- `catalog`
- `inventory`
- `reservations`
- `orders`
- `payments`
- `shipping`
- `settings`
- `reports`

Modules should have clear boundaries.

A typical module may contain:

```text
modules/
└── inventory/
    ├── inventory.routes.ts
    ├── inventory.schemas.ts
    ├── inventory.service.ts
    └── inventory.repository.ts
```

Use only the layers necessary for each feature.

Responsibilities:

- Routes: endpoint registration and HTTP handling.
- Schemas: request and response validation.
- Services: business operations and workflows.
- Repositories: persistence queries and database access.

Avoid adding empty layers merely to follow a pattern.

Cross-module operations should use explicit service boundaries rather than directly manipulating another module's internal implementation.

Shared infrastructure belongs outside feature modules.

## 6. Shared Packages

### packages/database

Responsible for:

- Drizzle schema definitions.
- Database connection infrastructure.
- Schema constraints.
- Database migrations.
- Database-related utilities.

Only trusted server-side applications may access the database connection and credentials.

Do not import server database infrastructure into browser applications.

Feature-specific persistence rules should remain appropriately encapsulated in their backend modules.

### packages/shared

Responsible for code safely shared across applications:

- Shared TypeScript types.
- Public API contracts.
- Common constants.
- Reusable validation schemas when appropriate.

Do not place server secrets, database connections or business services in this package.

Shared contracts must not expose private database implementation details.

### packages/ui

Responsible for custom-built UI primitives and shared styling foundations.

Potential components:

- Button.
- Card.
- Input.
- Select.
- Badge.
- Modal.
- Shared layout primitives, when appropriate.

Use React for components that must be shared between React applications or React islands.

Keep Astro-specific layout components inside the storefront unless a justified sharing mechanism is established.

Do not introduce external component generators.

Only move components into this package when they have a clear reusable responsibility.

## 7. Custom UI Architecture

UI code is divided into three conceptual levels.

### Level 1 — Design Foundations

Define:

- Colors.
- Typography.
- Spacing.
- Border radii.
- Shadows.
- Breakpoints.
- Light and dark theme conventions, if adopted.

Use CSS variables and Tailwind CSS where appropriate.

### Level 2 — UI and Layout Primitives

Build reusable components such as:

- Container.
- Section.
- Card.
- Button.
- Input.
- Stack.
- Grid.

Components should have small, predictable APIs.

Avoid excessive configuration and abstractions.

### Level 3 — Feature Components

Create components that understand a specific application feature.

Examples:

- ProductCard.
- ProductGallery.
- ShoppingCart.
- OrderSummary.
- ReservationTimer.
- AdminSidebar.

Keep feature components close to their owning feature.

The storefront Navbar and Footer are application layout components, not automatically global UI primitives.

Prefer composition over highly configurable components.

## 8. Database Architecture

PostgreSQL is the system of record.

Initial domain entities are expected to include:

- Administrators.
- Products.
- Product variants.
- Product images.
- Brands.
- Categories.
- Product-category relationships.
- Inventory records and movements.
- Reservations.
- Orders.
- Order items.
- Payments.
- Delivery methods.
- Shipment or fulfillment records.
- Store settings.

The exact table structure, constraints and relations will be defined before database implementation.

### Data Integrity

- Use primary and foreign keys.
- Add uniqueness and check constraints where appropriate.
- Use transactions for inventory-sensitive operations.
- Prevent negative available stock.
- Use database-backed concurrency control.
- Never rely exclusively on frontend state for inventory correctness.
- Maintain historical order information independently of subsequent product edits.
- Store monetary amounts in integer minor units or another explicitly defined exact representation.
- Use consistent timestamps and timezone handling.
- Generate and version schema migrations.

Do not edit migrations already applied to shared or production environments.

## 9. Inventory and Reservations

Inventory must support both website purchases and externally coordinated sales.

Available stock must account for active reservations.

### Manual Reservations

- Created or managed by an administrator.
- Default expiration: 10 minutes.
- Optional expiration: 5 minutes.
- Can be extended or cancelled.
- Reduce available stock while active.
- Can become confirmed sales after payment verification.

An expired unpaid reservation releases its stock safely.

Expiration must be enforced by the backend/database, not solely by a frontend countdown.

A scheduled cleanup process may be used in addition to expiration checks during stock operations.

### Automatic Reservations

Online checkout creates reservations appropriate to the selected payment flow.

Payment confirmation, reservation expiration and cancellation must be coordinated to prevent overselling.

Late payment confirmations require explicit handling and must never silently generate negative inventory.

Concurrent reservation attempts must use safe database operations.

## 10. Orders and Payment Architecture

Orders represent purchase attempts and confirmed sales.

Keep the order lifecycle separate from the payment lifecycle.

The payment module must expose a provider-independent integration boundary.

### Mercado Pago

Initial automatic payment provider: Mercado Pago Checkout Pro.

Flow:

1. Backend validates the cart.
2. Backend creates a pending order and reserves stock.
3. Backend creates a payment preference.
4. Customer completes payment through Mercado Pago.
5. Backend receives and verifies payment notifications.
6. Backend reconciles payment and order state.
7. Approved payments finalize the sale.

Webhook processing must be authenticated or verified according to provider requirements and idempotent.

Browser redirects alone must not confirm payment.

### Bank Transfers

Bank transfers remain manual.

- Customers coordinate the transfer through WhatsApp.
- The administrator reserves inventory.
- Payment details are shared privately.
- The administrator verifies receipt.
- The administrator confirms payment in the dashboard.

Opening WhatsApp alone must not automatically reserve stock indefinitely.

Late transfers, failed payments and released reservations require manual exception handling when appropriate.

### Future Providers

The payment integration must allow new provider adapters.

Do not implement Bamboo or other gateways until required.

Avoid coupling order services to provider-specific response formats.

## 11. Delivery and Fulfillment

Initial delivery methods:

- Pickup by arrangement.
- Paid local delivery.
- Paid domestic shipping.

The backend determines final shipping fees based on configured delivery rules.

The checkout must display the complete amount before payment.

Shipping and fulfillment state must be tracked independently from payment confirmation.

External courier integrations are not required initially.

## 12. API and Client Communication

Both frontend applications communicate with Fastify through documented API contracts.

Guidelines:

- Use consistent versioned routes.
- Validate all external input.
- Return consistent error responses.
- Use explicit authorization for protected operations.
- Avoid leaking internal database structures.
- Share public contracts through `packages/shared` where useful.
- Keep API requests and server state access organized within each frontend.

Do not duplicate authoritative business logic in clients.

## 13. Authentication and Security

Initial administrative access is limited to authorized store operators.

Customer accounts are not required.

Security requirements:

- Secure administrator authentication.
- Server-side authorization checks.
- Protected administrative endpoints.
- Secure session or token handling.
- Appropriate cookie and CSRF protections for the chosen authentication strategy.
- Secrets stored in environment variables.
- No service-role or payment credentials exposed to browsers.
- Secure image upload authorization.
- Verified external payment notifications.
- Appropriate input validation and rate limiting.

The final authentication strategy will be selected during implementation.

## 14. Testing and Verification

Use focused automated tests for critical business operations.

Priority areas:

- Inventory calculations.
- Concurrent reservations.
- Reservation expiration.
- Order state transitions.
- Payment webhook idempotency.
- Manual payment confirmation.
- Authorization and protected endpoints.

Add integration tests for database operations where needed.

Use typechecking, linting and focused builds.

Do not require full-project builds for unrelated small changes unless necessary.

## 15. Deployment Architecture

All applications remain in one Git repository.

They may be deployed as separate runtime targets:

- Public storefront.
- Administration dashboard.
- Backend API.
- Managed PostgreSQL database.
- Managed image storage.

A monorepo does not require all applications to run in one process or on one server.

Hosting, deployment providers, domains, HTTPS and CI/CD will be finalized before production.

Never assume that a shared hosting plan supports persistent Node.js processes without verification.

## 16. Future Reusability

The initial backend belongs to Racoon Store.

Do not create a universal commerce framework.

Favor clear feature boundaries and reusable infrastructure.

If a future project needs an existing module, evaluate extraction into a reusable package based on actual requirements.

Do not share production databases or business data between unrelated projects by default.

## 17. Implementation Strategy

Development proceeds incrementally:

1. Initialize monorepo and development tooling.
2. Establish database and API foundations.
3. Implement product management end-to-end.
4. Implement inventory and reservations.
5. Implement orders and payment flows.
6. Implement delivery management.
7. Complete storefront UI and admin workflows.
8. Run integration, security and deployment verification.

Avoid implementing an entire layer before any end-to-end functionality can be tested.

Every implemented module should have clear acceptance criteria and verification.

Documentation should reflect actual implementation, not only planned architecture.
