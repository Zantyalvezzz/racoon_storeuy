# Racoon Store — UI Guidelines

**Status:** Planning  
**Version:** 0.1  
**Scope:** Storefront, admin dashboard, design foundations and reusable UI components.

## 1. Design Philosophy

Racoon Store uses a custom-built UI system developed specifically for this project.

Our principles are:

- Build reusable components ourselves.
- Prefer clarity and simplicity over unnecessary abstractions.
- Maintain visual consistency across the project.
- Design mobile-first, especially for the storefront.
- Follow accessibility and semantic HTML practices.
- Keep layouts flexible and easy to maintain.
- Reuse established components instead of duplicating functionality.
- Avoid adding dependencies when native browser or framework capabilities are sufficient.

Do not use shadcn/ui, UI component generators or complete third-party component systems unless explicitly approved.

Tailwind CSS is the primary styling tool. Custom CSS is allowed when it offers a clearer solution.

## 2. Design Foundations

The UI system must establish consistent design foundations before developing complex interfaces.

### 2.1 Colors

Use semantic color tokens instead of hardcoding arbitrary colors throughout components.

Suggested token roles:

- Background.
- Surface.
- Foreground.
- Muted foreground.
- Primary.
- Secondary.
- Border.
- Success.
- Warning.
- Danger.

The actual color palette will be selected during visual design.

Do not invent a final brand palette during initial scaffolding.

### 2.2 Typography

Establish a consistent typography system covering:

- Page titles.
- Section headings.
- Body text.
- Secondary text.
- Labels.
- Captions.

Use a limited number of font families, sizes and weights.

Typography should remain readable across mobile and desktop screens.

### 2.3 Spacing

Use a consistent spacing scale.

Prefer established spacing tokens or Tailwind spacing utilities over unrelated arbitrary values.

Spacing should communicate hierarchy and relationships between elements.

Avoid excessive wrappers introduced only for spacing.

### 2.4 Borders, Radius and Shadows

Define consistent styles for:

- Cards.
- Inputs.
- Buttons.
- Dropdowns.
- Dialogs.
- Panels.

Use visual effects intentionally.

Avoid inconsistent border radii, shadows and decorative effects between similar components.

### 2.5 Design Tokens

Prefer CSS custom properties for shared semantic design values.

Tailwind should consume the established tokens where appropriate.

The storefront and admin dashboard may share brand foundations while keeping application-specific styling.

Do not create a separate styling framework.

## 3. Component Architecture

Components are organized conceptually into three levels.

### Level 1 — Design Foundations

Shared visual definitions:

- Colors.
- Typography.
- Spacing.
- Breakpoints.
- Borders.
- Shadows.
- Theme variables.

### Level 2 — UI and Layout Primitives

Generic components without business-specific knowledge.

Initial candidates:

- Container.
- Section.
- Button.
- Card.
- Input.
- Select.
- Badge.
- Modal.

Additional components should be introduced only when needed.

### Level 3 — Feature Components

Components specific to an application feature.

Storefront examples:

- ProductCard.
- ProductGallery.
- ProductFilters.
- ShoppingCart.
- CartItem.
- OrderSummary.

Admin examples:

- AdminSidebar.
- ProductForm.
- InventoryTable.
- ReservationTimer.
- OrderDetails.

Keep feature components close to the feature that owns them.

## 4. Component Responsibilities

Each component should have one clear responsibility.

Examples:

### Container

Controls horizontal content width, centering and padding.

It must not contain business logic.

### Section

Provides a consistent structural wrapper for page sections.

Do not add it when semantic HTML and existing utilities already provide a simpler solution.

### Card

Provides a reusable visual surface.

It must not contain product-specific behavior.

### ProductCard

Displays a product using appropriate UI primitives.

It may contain:

- Product image.
- Name.
- Price.
- Condition.
- Availability.
- Navigation to product details.

Product-specific presentation belongs here, not inside the generic Card component.

### Navbar and Footer

These are application layout components.

The public storefront and administration dashboard may have different navigation structures.

Do not force unrelated layouts into one highly configurable component.

## 5. Component Design Rules

- Use clear, descriptive component names.
- Prefer composition over extensive configuration.
- Keep props minimal and predictable.
- Use TypeScript to define component interfaces.
- Avoid boolean props that create many unrelated visual modes.
- Do not add variants before there is a real use case.
- Do not duplicate an existing component to make small styling changes.
- Do not create generic wrappers without a clear benefit.
- Keep business operations outside generic UI primitives.
- Avoid excessive nesting.
- Preserve accessibility and semantic behavior.

Prefer refactoring a component when its responsibilities become unclear.

Do not extract components based only on file size.

## 6. Astro and React Boundaries

The public storefront uses Astro with React islands.

### Astro Components

Prefer Astro for primarily static or server-rendered content.

Examples:

- Page layouts.
- Static navigation.
- Footer.
- Container.
- Section.
- Static product presentation.
- Editorial content.

Astro components use `.astro` files.

### React Components

Use React for interfaces requiring client-side interaction.

Examples:

- Shopping cart.
- Interactive filters.
- Quantity selection.
- Interactive forms.
- Dynamic checkout controls.

React components use `.tsx` files.

Do not hydrate React components unnecessarily.

An Astro page may render React components without client-side hydration when no browser interactivity is required.

### Shared UI Package

`packages/ui` may contain reusable React components when sharing between React applications or islands is justified.

Astro-specific components normally belong inside `apps/web`.

Do not convert static Astro components to React solely to achieve reuse.

Shared design tokens and styles may be reused independently of component frameworks.

## 7. Recommended Frontend Organization

### Public Storefront

Organize the storefront by application responsibility.

Illustrative structure:

```text
apps/web/src/
├── components/
│   ├── ui/
│   ├── layout/
│   └── features/
├── layouts/
├── pages/
├── styles/
└── lib/
```

Examples:

- `components/ui/Container.astro`
- `components/ui/Card.astro`
- `components/layout/Navbar.astro`
- `components/layout/Footer.astro`
- `components/features/products/ProductCard.astro`
- `components/features/cart/ShoppingCart.tsx`

This is an initial convention, not a requirement to generate every directory immediately.

### Administration Dashboard

Illustrative structure:

```text
apps/admin/src/
├── components/
│   ├── ui/
│   └── layout/
├── features/
├── pages/
├── styles/
└── lib/
```

Feature-specific components belong inside their respective feature folders when appropriate.

### Shared Components

Move components into `packages/ui` only when there is a demonstrated need for reuse.

Do not maintain duplicate copies of shared components after extracting them.

## 8. Component Naming

Use PascalCase for component names.

Examples:

- `ProductCard.astro`
- `Navbar.astro`
- `Button.tsx`
- `ReservationTimer.tsx`

Component names should describe their purpose.

Use consistent naming for styles, hooks, utilities and feature modules.

Avoid vague names such as `CustomBox`, `GeneralComponent` or `Component2`.

## 9. Styling Conventions

Tailwind CSS is the primary styling approach.

Rules:

- Prefer established design tokens.
- Keep utility classes readable.
- Avoid excessive arbitrary values.
- Avoid repeating large combinations of classes without justification.
- Use CSS for complex styling when it improves maintainability.
- Do not introduce CSS-in-JS libraries without approval.
- Do not mix multiple competing styling systems.
- Avoid global styles that unexpectedly affect unrelated components.

Use semantic component variants only when there is an actual design requirement.

## 10. Responsive Design

Build the storefront mobile-first.

Priorities:

- Comfortable navigation on small screens.
- Responsive product grids.
- Appropriate image sizing.
- Readable typography.
- Accessible touch targets.
- Usable product filters.
- Clear purchase actions.
- Checkout forms that work on mobile devices.

Avoid horizontal overflow.

Use responsive layouts based on content requirements rather than arbitrary device categories.

The admin dashboard must also remain usable on smaller screens, even if its primary use is desktop administration.

## 11. Accessibility

Components must follow accessible interaction patterns.

Requirements:

- Use semantic HTML.
- Provide labels for form controls.
- Provide meaningful image alternative text.
- Support keyboard navigation.
- Maintain visible focus indicators.
- Use accessible contrast.
- Use buttons for actions and links for navigation.
- Provide meaningful loading, error and disabled states.
- Avoid relying only on color to communicate status.
- Handle dialog focus appropriately.

Accessibility must be considered when building components, not added only at the end.

## 12. Product Presentation

Product information should be displayed consistently across the storefront.

Product cards should prioritize:

- Product photography.
- Product name.
- Price.
- Relevant size or condition details.
- Availability.

Avoid overcrowding cards with unnecessary information.

Product detail pages may provide additional measurements, photographs and descriptions.

Unavailable products must not present an enabled purchase action.

Actual availability is still validated by the backend during checkout.

## 13. Forms and Feedback

Forms must provide clear feedback.

Use consistent patterns for:

- Field labels.
- Validation errors.
- Required fields.
- Submission states.
- Success messages.
- Loading indicators.
- Disabled controls.

Do not rely on browser-side validation alone for business-critical operations.

Admin forms should communicate the result of inventory and payment operations clearly.

Destructive operations may require confirmation when appropriate.

## 14. Loading, Empty and Error States

Every data-driven feature should consider:

- Loading.
- Empty results.
- Successful results.
- Recoverable errors.
- Unavailable services.

Examples:

- No products match the selected filters.
- A product becomes unavailable.
- The shopping cart is empty.
- A reservation expires.
- A payment requires verification.

Avoid interfaces that fail silently.

## 15. Visual Consistency

The storefront and dashboard should share fundamental design decisions without requiring identical layouts.

The storefront prioritizes:

- Product presentation.
- Brand identity.
- Shopping experience.
- Mobile navigation.

The dashboard prioritizes:

- Information density.
- Operational clarity.
- Forms and tables.
- Fast administrative workflows.

Visual consistency must not reduce usability.

## 16. Component Development Workflow

When creating or modifying a UI component:

1. Identify the actual use case.
2. Check for an existing component.
3. Decide whether the component belongs to Astro, React or the shared package.
4. Define its responsibility and minimal props.
5. Implement semantic markup and styling.
6. Consider mobile behavior and accessibility.
7. Verify relevant interaction states.
8. Test integration within the owning feature.
9. Extract shared behavior only when justified.

Do not generate a complete component library upfront.

The initial components will be developed incrementally as the first pages are built.

## 17. AI Development Instructions

When OpenCode works on UI tasks:

- Follow this document and the relevant architecture rules.
- Do not introduce shadcn/ui or component generators.
- Prefer existing components.
- Respect Astro and React boundaries.
- Implement only the requested component or feature.
- Do not redesign unrelated pages.
- Avoid speculative abstractions.
- Do not add unnecessary dependencies.
- Report changed files and verification results.

The developer retains control over component API and design decisions.

## 18. Pending Visual Decisions

The following will be defined during visual design:

- Brand color palette.
- Typography choices.
- Logo and brand assets.
- Light and dark theme requirements.
- Exact spacing and sizing scales.
- Initial page layouts.
- Product card visual treatment.
- Navbar and footer design.
- Dashboard visual identity.

Do not treat undecided visual choices as established project requirements.
