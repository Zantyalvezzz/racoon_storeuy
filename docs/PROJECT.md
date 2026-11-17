# Racoon Store — Project Definition

**Status:** Planning  
**Version:** 0.1  
**Project type:** Single-store e-commerce platform

## 1. Overview

Racoon Store is an online store for clothing, footwear, and accessories, primarily sourced from the United States.

The platform will include:

- A public storefront for browsing and purchasing products.
- A private administration dashboard.
- A backend API managing business operations.
- A PostgreSQL database.
- Online payments and manually coordinated sales.

The initial system serves one store, but its architecture should support future improvements without unnecessary complexity.

## 2. Main Objectives

- Establish an independent online sales channel.
- Centralize product and inventory management.
- Allow customers to purchase without creating accounts.
- Support automatic and manually coordinated payments.
- Prevent overselling products with limited stock.
- Manage sales originating from Instagram, Facebook, WhatsApp and the website.
- Keep everyday operations manageable through the admin dashboard.
- Build maintainable software that can evolve over time.

## 3. Public Storefront

### Product Catalog

Customers can:

- Browse available products.
- Search and filter by category, brand, gender, size, condition and price.
- View product photos, descriptions, measurements and prices.
- Select available product variants.
- See availability and promotional prices when applicable.

Products can belong to multiple categories where appropriate.

### Product Details

Product pages display:

- Product name and brand.
- Images.
- Price.
- Condition: new or used.
- Size and measurements, where applicable.
- Description.
- Available stock.
- Purchase and contact options.

### Shopping Cart and Checkout

Customers can:

- Add available products to their cart.
- Change quantities or remove products.
- Select a delivery method.
- View shipping costs and the final total.
- Choose a payment method.
- Complete purchases without registering an account.

The backend must validate prices and availability before creating a payable order.

## 4. Product and Inventory Management

### New Products

New products may share one listing with multiple sizes and independent stock quantities per size.

### Used Products

Each used item has its own listing, images, condition, measurements, price and one initial stock unit.

### Inventory

Inventory must reflect:

- Physical quantities.
- Available quantities.
- Reserved quantities.
- Completed sales.
- Manual stock adjustments.

Inventory is shared across website sales and externally coordinated sales.

Detailed rules will be maintained in `docs/BUSINESS_RULES.md`.

## 5. Payment Methods

### Mercado Pago

The store will use Mercado Pago Checkout Pro for online payments.

- Payment initiation occurs through the backend.
- Payment status must be verified server-side.
- Confirmed payments update orders and inventory.
- Failed, cancelled and pending payments must be handled safely.

### Bank Transfer

Bank transfers are coordinated manually through WhatsApp.

- Customers initiate contact with their selected products or order details.
- The administrator reserves stock before sharing payment instructions.
- The administrator verifies receipt of the funds.
- Payment confirmation and order completion are managed through the dashboard.

Automated bank transfer reconciliation is outside the initial scope.

### Future Payment Providers

The payment architecture must allow additional providers, such as Bamboo, without rebuilding the ordering system.

No additional payment gateway will be implemented initially.

## 6. Reservation System

The system supports automatic and manual stock reservations.

Manual reservations:

- Default duration: 10 minutes.
- Optional duration: 5 minutes.
- Can be extended or cancelled by the administrator.
- Temporarily reduce available inventory.
- Expire automatically when unpaid.
- Can be confirmed as sold after payment verification.

Expired reservations must not cause overselling.

Late bank transfers require manual review and reconciliation.

Mercado Pago reservations may follow a different expiration policy appropriate to payment processing.

## 7. Delivery Methods

The initial delivery options are:

- Free pickup by arrangement.
- Paid local delivery.
- Paid shipping to other regions of Uruguay.

Shipping charges are paid by the customer and included in the checkout total when applicable.

The administrator can configure delivery options and prices.

Courier APIs and automated shipping labels are not required initially.

## 8. Administration Dashboard

The private dashboard must provide control over:

### Catalog
- Products and variants.
- Categories and brands.
- Prices and promotions.
- Photos and descriptions.
- Product visibility.

### Inventory
- Stock quantities.
- Manual reservations.
- Reservation expiration.
- Stock adjustments.
- External sales.

### Orders and Payments
- Order details and statuses.
- Mercado Pago transactions.
- Manual transfer confirmations.
- Order cancellations.
- Payment-related exceptions.

### Fulfillment
- Delivery methods and fees.
- Order preparation.
- Shipment and delivery status.

### Store Settings
- Basic store information.
- Contact information.
- Configurable operational settings.

### Reporting
- Basic sales summaries.
- Revenue and order counts.
- Product sales information.

## 9. User Roles

The initial system has:

- Public customers without required registration.
- One authenticated store administrator.

Additional staff accounts, permissions and customer accounts can be considered later.

## 10. Initial Technical Stack

- pnpm workspaces.
- TypeScript.
- Astro + React for the storefront.
- React + Vite for the admin dashboard.
- Node.js + Fastify for the API.
- PostgreSQL hosted on Supabase.
- Drizzle ORM.
- Tailwind CSS and custom-built components.
- Mercado Pago Checkout Pro.

The repository follows a modular monolith architecture.

## 11. Non-Functional Requirements

- Responsive, mobile-first interface.
- Accessible user interactions.
- Fast product pages and appropriate SEO.
- Secure administrative authentication.
- Server-side authorization and validation.
- Reliable inventory and payment consistency.
- Maintainable, modular and testable code.
- No secrets exposed to public clients.
- Safe database migrations.
- Clear handling of operational errors.

## 12. Outside Initial Scope

The following features are not required for the initial release:

- Customer accounts.
- Loyalty programs.
- Multiple independent stores.
- Multi-vendor marketplaces.
- Automated banking reconciliation.
- Bamboo or other additional payment gateways.
- Courier API integrations.
- Advanced analytics.
- Automated supplier or eBay synchronization.

These features may be evaluated later.

## 13. Pending Decisions

The following details will be defined during planning or implementation:

- Store visual identity and branding.
- Product image requirements.
- Exact shipping tariffs and delivery areas.
- Pickup arrangements.
- Returns and exchange policies.
- Initial hosting and deployment strategy.
- Detailed admin authentication implementation.
- Payment and delivery notification requirements.

Pending decisions must not be treated as implemented features.

## 14. Success Criteria

The initial release is complete when the administrator can:

1. Create and publish a product.
2. Manage its images, variants and stock.
3. Receive an online purchase through Mercado Pago.
4. Verify the payment and process the order.
5. Reserve a product for a manually coordinated sale.
6. Confirm a bank transfer and mark the product as sold.
7. Manage delivery or pickup.
8. Prevent simultaneous purchases from overselling the same stock.

All core operations must work through the application without manually editing the database or application code.
