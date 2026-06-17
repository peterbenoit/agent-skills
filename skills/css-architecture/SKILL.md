---
name: css-architecture
description: Structures and audits CSS for maintainability and correctness. Use when writing, reviewing, or refactoring CSS. Use when the user mentions specificity, cascade, custom properties, CSS variables, BEM, ITCSS, layout issues, media queries, or asks to audit or organize stylesheets.
---

# CSS Architecture

## Overview

CSS without a deliberate architecture becomes unmaintainable fast. The goal is CSS that is predictable (same class always produces same result), scalable (adding a component does not break existing ones), and debuggable (you can trace any visual property to its source in under a minute).

## When to Use

- Starting a new stylesheet or design system
- Auditing existing CSS for specificity wars or dead code
- Refactoring disorganized styles
- Implementing design tokens / custom properties
- Debugging cascade or specificity conflicts

## Architecture: ITCSS Layer Order

Structure stylesheets in specificity order, low to high. Import or `@layer` in this order:

```
1. Settings     — design tokens, custom properties only. No output CSS.
2. Tools        — mixins, functions (preprocessor only). No output CSS.
3. Generic      — reset/normalize. Low specificity.
4. Elements     — bare HTML elements (h1, a, p). No classes.
5. Objects      — layout patterns (grid, container). Class-based, no cosmetics.
6. Components   — UI components. Scoped, self-contained.
7. Utilities    — single-purpose overrides. High specificity intentional.
```

With native CSS layers:

```css
@layer settings, generic, elements, objects, components, utilities;

@layer settings {
  :root {
    --color-primary: #005ea2;
    --space-md: 1rem;
  }
}

@layer components {
  .card { ... }
}

@layer utilities {
  .sr-only { ... }
}
```

## Custom Properties (Design Tokens)

Always define tokens on `:root`. Name by role, not value.

```css
/* Wrong — describes the value */
--blue-500: #005ea2;

/* Right — describes the role */
--color-action: #005ea2;
--color-action-hover: #1a4480;
```

Use semantic aliases that reference primitive tokens:

```css
:root {
  /* Primitives */
  --_blue-500: #005ea2;
  --_blue-700: #1a4480;

  /* Semantic */
  --color-link: var(--_blue-500);
  --color-link-hover: var(--_blue-700);
}
```

Scope component-level tokens with the component name:

```css
.card {
  --card-padding: var(--space-md);
  --card-bg: var(--color-surface);
  padding: var(--card-padding);
  background: var(--card-bg);
}
```

## Specificity Rules

- Prefer class selectors. Avoid ID selectors in stylesheets.
- Never use `!important` except in utility classes (intentional override) and resets.
- Never chain more than two classes for specificity (`.parent .child` is fine; `.a .b .c` is a smell).
- Specificity score to target: (0, 1, 0) for components. If you need higher, the architecture is wrong.

```css
/* Good — (0,1,0) */
.button { }

/* Smell — (0,2,0) unnecessarily */
.nav .button { }

/* Fix — scope with a modifier instead */
.button--nav { }
```

## Naming: BEM

```css
/* Block */
.card { }

/* Element — part of the block */
.card__title { }
.card__body { }
.card__footer { }

/* Modifier — variant of block or element */
.card--featured { }
.card__title--large { }
```

State is separate from BEM — use a state class:

```css
.card.is-expanded { }
.button.is-loading { }
```

## Layout Patterns

### Container

```css
.container {
  width: min(100% - 2rem, 75rem);
  margin-inline: auto;
}
```

### Grid

```css
/* Auto-fill responsive columns */
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(min(100%, 20rem), 1fr));
  gap: var(--space-md);
}
```

### Stack (vertical rhythm)

```css
.stack > * + * {
  margin-top: var(--stack-gap, var(--space-md));
}
```

### Cluster (horizontal wrapping)

```css
.cluster {
  display: flex;
  flex-wrap: wrap;
  gap: var(--cluster-gap, var(--space-sm));
  align-items: center;
}
```

## Responsive Design

Use `min-width` media queries (mobile-first). Define breakpoints as custom properties in JS or use `@custom-media` (with PostCSS):

```css
/* Breakpoints as named thresholds, not device names */
@media (width >= 40rem) { /* tablet+ */ }
@media (width >= 64rem) { /* desktop+ */ }
```

Prefer `clamp()` for fluid sizing over multiple breakpoints:

```css
font-size: clamp(1rem, 2.5vw, 1.5rem);
padding: clamp(1rem, 5%, 3rem);
```

## Performance

- Keep stylesheet specificity flat — fewer overrides at runtime
- Avoid `@import` in production (blocks parallel download); use a build step to bundle
- Use `content-visibility: auto` on off-screen sections for large pages
- Scope animations: `will-change: transform` only on elements that actually animate, remove after animation ends
- Prefer CSS transitions over JS-driven animations for simple state changes

## Common Smells

| Smell | Fix |
|---|---|
| `!important` in a component | Reduce specificity of the overridden rule |
| `.parent .child .grandchild` selector | Flatten; use BEM modifier or direct child |
| Duplicate `color` declarations for same concept | Extract to a custom property |
| Magic numbers (`margin-top: 23px`) | Replace with a spacing token |
| `z-index: 9999` | Create a z-index scale as custom properties |
| Media query values not matching the breakpoint system | Standardize to the defined breakpoints |

## Audit Checklist

- [ ] Custom properties defined in `:root` for all design tokens
- [ ] No ID selectors in component styles
- [ ] No `!important` outside utilities and resets
- [ ] BEM (or equivalent) naming applied consistently
- [ ] Layer order established (ITCSS or `@layer`)
- [ ] No duplicate property declarations for the same concept
- [ ] Breakpoints match a defined scale
- [ ] No magic numbers — all values trace to a token
- [ ] Dead CSS removed (use browser coverage tab to identify)
