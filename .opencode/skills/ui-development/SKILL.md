
---
name: ui-development
description: Develop or review UI components, layouts, styling, accessibility, and frontend presentation for Racoon Store. Use for Astro, React, and custom component system tasks.
---

# UI Development

## Context

Follow:
- AGENTS.md
- docs/ARCHITECTURE.md
- docs/UI_GUIDELINES.md

Racoon Store uses Astro, React, TypeScript, and Tailwind CSS.

The UI system is custom-built, not generated from a component library.

## Developer Ownership

The developer wants to design and implement core UI components personally.

- Do not generate new UI components unless explicitly requested.
- For planning tasks, explain component responsibilities and possible APIs without implementing them.
- For reviews, inspect the supplied code and identify concrete improvements.
- Preserve the developer's intended visual design.
- Do not redesign existing interfaces without approval.
- Do not introduce shadcn/ui or component generators.

## Component Boundaries

Distinguish between:

1. Design foundations: tokens, colors, typography, spacing.
2. UI primitives: Container, Button, Card, Input.
3. Feature components: ProductCard, ShoppingCart, ProductForm.

Keep business-specific components within their owning application or feature.

Extract components into packages/ui only when actual cross-application reuse justifies it.

## Astro and React

- Prefer Astro for static layouts and presentation.
- Use React for client-side interaction.
- Avoid unnecessary React hydration.
- Do not convert Astro components to React solely for reuse.
- Follow established file and naming conventions.

## Component Quality

When implementing or reviewing:

1. Identify the component's responsibility.
2. Inspect relevant existing patterns.
3. Check props and TypeScript types.
4. Prefer semantic HTML.
5. Verify keyboard accessibility and focus behavior.
6. Check responsive layouts.
7. Consider loading, empty, error, and disabled states when relevant.
8. Avoid unnecessary abstractions and configuration.

Do not create generic components without a demonstrated need.

## Styling

- Use Tailwind CSS and established design tokens.
- Keep styling consistent.
- Avoid arbitrary values without justification.
- Use custom CSS when it improves clarity.
- Avoid competing styling approaches.

## Verification

Run the smallest relevant checks available.

Report:
- Components reviewed or changed.
- Issues found.
- Verification performed.
- Remaining concerns.

Do not modify unrelated components.
Do not commit or push without approval.
