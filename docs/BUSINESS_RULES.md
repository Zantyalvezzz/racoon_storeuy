# Racoon Store — Business Rules

**Status:** Planning  
**Version:** 0.1  
**Scope:** Products, inventory, reservations, orders, payments and fulfillment.

## 1. General Principles

Racoon Store operates as a single-store e-commerce business.

The backend and PostgreSQL database are the authoritative sources for inventory, orders, payments and reservations.

Business rules must apply consistently across:

- Public storefront purchases.
- Instagram sales.
- Facebook sales.
- WhatsApp sales.
- Administrative operations.

The system must prevent overselling, invalid payment confirmations and inconsistent stock updates.

## 2. Product Rules

### 2.1 Product Types

Products are classified by condition:

- New.
- Used.

Condition is a required attribute.

### 2.2 New Products

New products may share one listing with multiple size variants.

Each variant has its own stock quantity.

Example:

Nike hoodie:
- Size S: 2 units.
- Size M: 3 units.
- Size L: 1 unit.

All variants belong to the same product listing.

A variant must be uniquely identifiable for inventory and order operations.

### 2.3 Used Products

Each used item has its own individual listing.

Rules:

- Initial quantity is exactly 1.
- The item has its own photos.
- Its condition and measurements are individually documented.
- It has its own price.
- Once sold, its available quantity becomes 0.
- Used listings cannot represent multiple interchangeable units.

A second used item should be created as a separate listing.

### 2.4 Product Information

Products may contain:

- Name.
- Description.
- Brand.
- Categories.
- Gender classification.
- Condition.
- Price.
- Promotional price.
- Images.
- Size information.
- Measurements.
- Stock information.

A product may belong to multiple categories.

Required fields and category taxonomies will be finalized during catalog implementation.

### 2.5 Product Visibility

Products can be:

- Draft.
- Published.
- Archived.

Only published products are publicly accessible through the normal catalog.

Publication status and stock availability are separate concepts.

A published product with no available stock must not be purchasable.

Whether unavailable products remain visible will be determined through storefront settings.

## 3. Pricing Rules

The initial store currency is Uruguayan pesos (UYU).

Rules:

- Product prices must be non-negative.
- Monetary calculations must use exact representations.
- The backend determines authoritative prices.
- The checkout cannot trust prices supplied by the browser.
- The customer must see the final payable total before payment.
- Shipping costs are added when applicable.

The listed product price is the same regardless of the selected payment method.

Payment processing commissions are treated as business expenses.

Prices and product descriptions recorded in an order must remain historically accurate, even if the original product is subsequently edited.

## 4. Inventory Rules

### 4.1 Inventory Concepts

The system distinguishes:

**Physical stock:** Units physically held by the store.

**Reserved stock:** Units temporarily held for unpaid orders or manual reservations.

**Committed stock:** Units assigned to confirmed sales but not yet handed over or dispatched.

**Available stock:** Units that can currently be purchased.

Available stock is calculated as:

Physical stock - Reserved stock - Committed stock.

The implementation must ensure stock is never deducted twice during reservation conversion or fulfillment.

### 4.2 Inventory Consistency

Rules:

- Available stock must never be negative.
- Every reservation must reference a specific product or variant.
- Stock changes must be validated server-side.
- Concurrent operations must be protected through database transactions and appropriate locking or atomic updates.
- A browser cart does not guarantee availability.
- Availability must be revalidated before creating an order and reserving stock.

### 4.3 Manual Stock Adjustments

Administrators can adjust stock when necessary.

Each adjustment should record:

- Product or variant.
- Previous quantity.
- New quantity or quantity change.
- Reason.
- Timestamp.
- Responsible administrator.

Adjustments must not invalidate active reservations or confirmed orders.

## 5. Manual Reservations

### 5.1 Creation

Administrators can reserve stock through the dashboard.

Reservations may originate from:

- WhatsApp.
- Instagram.
- Facebook.
- Other external sales.

The administrator must reserve the product before promising its availability or sharing transfer instructions for a confirmed purchase arrangement.

Creating a WhatsApp inquiry does not automatically reserve stock.

### 5.2 Reservation Duration

The default duration is 10 minutes.

Administrators can also select 5 minutes.

Each reservation records:

- Product or variant.
- Quantity.
- Creation time.
- Expiration time.
- Reservation status.
- Associated order or reference, when applicable.

A reservation immediately reduces available stock.

### 5.3 Reservation States

Reservations may be:

- Active.
- Converted to a confirmed sale.
- Expired.
- Cancelled.

An expired or cancelled reservation releases its stock.

A converted reservation must not release stock back into the available inventory.

### 5.4 Reservation Expiration

Expired unpaid reservations must no longer block available stock.

Expiration is enforced by the backend/database, not exclusively by a frontend timer.

Expiration may be processed through a scheduled job, transaction-time checks or a combination of both.

The system must prevent an expired reservation from being confirmed concurrently with its release.

### 5.5 Extension

Administrators can extend active reservations.

Extensions must update the expiration time safely.

A reservation must not be extended after its stock has been assigned elsewhere.

### 5.6 Late Transfers

If a transfer arrives after the reservation expires:

- The payment requires manual review.
- The original reservation is not automatically restored.
- The system checks whether the product remains available.
- If unavailable, the administrator must resolve the situation with the customer, including a refund where appropriate.

Late transfers must never cause overselling.

## 6. Online Checkout Reservations

### 6.1 Shopping Cart

Customers can place products in a cart without reserving stock.

Cart quantities must be validated before checkout.

### 6.2 Checkout Initiation

Before initiating a payment:

1. Validate product availability.
2. Validate current product prices.
3. Validate delivery fees.
4. Create a pending order.
5. Reserve the required stock.
6. Initiate the selected payment flow.

Reservation creation and inventory updates must be atomic.

### 6.3 Mercado Pago Reservations

Online reservations use a payment-appropriate expiration policy rather than the manual 5- or 10-minute rule.

The implementation must account for:

- Payment approval.
- Payment rejection.
- Payment cancellation.
- Pending or delayed payment confirmation.
- Customer abandonment.
- Late payment notifications.

Reservations must not be released without a safe policy for pending payments that could still be approved.

If a payment is approved after stock was legitimately released, it must enter an exception-handling process rather than silently overselling.

## 7. Orders

### 7.1 Order Creation

An order contains:

- Unique order identifier.
- Purchased product variants.
- Quantities.
- Unit prices at purchase time.
- Subtotal.
- Shipping amount.
- Final total.
- Selected payment method.
- Payment status.
- Order status.
- Delivery information.
- Creation and update timestamps.

Customers can purchase without registering an account.

### 7.2 Order Status

Order lifecycle states may include:

- Pending payment.
- Confirmed.
- Cancelled.
- Completed.

Payment and fulfillment states must be tracked separately.

The final state machine will be defined before implementing the orders module.

### 7.3 Order Integrity

- Order totals are calculated by the backend.
- Confirmed order details are preserved.
- Repeated requests must not generate duplicate confirmed sales.
- Order cancellation must follow explicit stock and payment rules.
- Invalid state transitions must be rejected.

## 8. Payments

### 8.1 Supported Methods

Initial supported methods:

1. Mercado Pago Checkout Pro.
2. Manually coordinated bank transfer.

Additional payment providers are outside the initial implementation.

### 8.2 Mercado Pago

Rules:

- The backend initiates payment preferences.
- Payment provider credentials remain server-side.
- Browser redirects do not prove payment approval.
- Payment notifications must be verified.
- Approved payments update order and inventory state.
- Duplicate notifications must not duplicate transactions or stock changes.
- Rejected and cancelled payments follow safe reservation-release rules.

### 8.3 Bank Transfers

Bank transfers are handled manually.

Flow:

1. Customer contacts the store.
2. Administrator verifies availability and reserves the product.
3. Administrator shares banking instructions.
4. Customer transfers the payment.
5. Administrator verifies the funds were received.
6. Administrator marks the payment as confirmed.
7. The order becomes confirmed and inventory is committed.

A payment screenshot is not sufficient evidence by itself.

Bank transfers must never be automatically marked as approved merely because the customer reports completing payment.

### 8.4 Payment Status

Payment records may have states such as:

- Pending.
- Approved.
- Rejected.
- Cancelled.
- Refunded.

Additional provider-specific statuses must be mapped to internal states where appropriate.

### 8.5 Payment Provider Independence

The orders module must not depend directly on Mercado Pago-specific response structures.

Provider integration details belong inside the payment module.

Future providers should implement compatible internal operations without requiring major changes to order and inventory logic.

## 9. Delivery and Pickup

### 9.1 Delivery Methods

Initial options:

- Pickup by arrangement.
- Local delivery.
- Domestic shipping.

Pickup has no shipping charge.

Delivery and shipping have configurable prices.

### 9.2 Shipping Charges

Shipping fees are the responsibility of the customer.

Rules:

- Delivery charges are calculated before payment.
- The final checkout total includes applicable delivery charges.
- No unconfirmed shipping amount may be charged as a finalized checkout total.
- Rates can be managed by the administrator.
- Courier integrations are not required initially.

### 9.3 Fulfillment

Orders may have fulfillment states such as:

- Not prepared.
- Preparing.
- Ready for pickup.
- Shipped.
- Delivered.

Fulfillment state is independent of payment status.

A paid order does not automatically mean the customer has received the product.

## 10. Administrative Operations

The administrator can:

- Create and update product listings.
- Manage product visibility.
- Manage variants and prices.
- Adjust inventory.
- Create, extend and cancel manual reservations.
- Register externally coordinated sales.
- Confirm received bank transfers.
- Review payment statuses.
- Manage orders.
- Manage delivery fees.
- Update fulfillment status.

Sensitive operations must require authentication and authorization.

Important inventory and financial actions should produce audit records.

## 11. Concurrency and Failure Handling

The system must safely handle:

- Two customers requesting the last unit simultaneously.
- A manual reservation while another customer starts checkout.
- Concurrent reservation expiration and payment confirmation.
- Duplicate Mercado Pago webhooks.
- Network failures during checkout.
- Repeated administrative submissions.
- Payments arriving after reservation expiration.

Critical operations must use transactions, uniqueness constraints, idempotency checks or other appropriate consistency mechanisms.

No failure should silently create negative stock or a duplicate confirmed sale.

## 12. Refunds and Cancellations

Refunds and cancellations must be treated as separate operations from stock availability.

A refund does not automatically mean the product is physically back in stock.

Restocking requires confirmation that the item can be sold again.

Detailed return and exchange policies will be defined before launch.

## 13. Business Rule Testing

Critical automated test scenarios must include:

1. Reserving the final available unit.
2. Preventing another reservation for that unit.
3. Expiring an unpaid reservation.
4. Extending an active reservation.
5. Converting a reservation into a confirmed sale.
6. Preventing duplicate payment confirmation.
7. Handling an approved payment after reservation expiration.
8. Preventing simultaneous checkout overselling.
9. Handling a late bank transfer.
10. Cancelling an order without incorrectly releasing committed stock.

These scenarios are part of the minimum reliability requirements.

## 14. Pending Business Decisions

The following require later confirmation:

- Exact shipping tariffs.
- Pickup locations and conditions.
- Final product visibility rules when sold out.
- Return and exchange policies.
- Mercado Pago payment-method configuration.
- Detailed timeout and reconciliation rules for online payments.
- Notification requirements.
- Final order and fulfillment state machines.

Until defined, the implementation must not invent irreversible business policies.
