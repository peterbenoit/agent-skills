---
name: uswds
description: Implements and customizes the U.S. Web Design System (USWDS). Use when building federal web properties, using USWDS components, configuring USWDS settings tokens, or ensuring compliance with federal web standards. Use when the user mentions USWDS, usa- prefix components, federal design system, or 21st Century IDEA Act.
---

# USWDS — U.S. Web Design System

## Overview

USWDS is the design system for U.S. federal government websites. It provides accessible, mobile-friendly components that comply with Section 508, WCAG 2.1 AA, and the 21st Century Integrated Digital Experience Act (21st Century IDEA). Do not override USWDS styles arbitrarily — customize through its token and settings system.

**Current stable version:** 3.x  
**Docs:** https://designsystem.digital.gov  
**Source:** https://github.com/uswds/uswds

## When to Use

- Building or maintaining a federal agency website
- Implementing USWDS components (headers, footers, alerts, cards, forms, etc.)
- Customizing USWDS through settings tokens
- Auditing a site for USWDS compliance
- Working with CMS themes (Drupal, WordPress) that implement USWDS

## Installation

```bash
npm install @uswds/uswds
```

With a Sass build pipeline:

```scss
// sass/styles.scss
@use "uswds-core" with (
  $theme-font-path: "../fonts",
  $theme-image-path: "../img",
  $theme-color-base-darkest: "gray-90",
  $theme-color-primary: "blue-60v",
  $theme-color-primary-dark: "blue-70v",
  $theme-color-primary-darker: "blue-80v",
);

@forward "uswds";
```

## Token System

USWDS uses a two-tier token system: system tokens (defined by USWDS) and theme tokens (your customization).

### Spacing

```scss
// Use USWDS spacing functions, not raw px
margin-bottom: units(2);    // 1rem (2 × 8px)
padding: units(1.5);        // 12px
gap: units(3);              // 1.5rem
```

### Color

```scss
// Always use color tokens, never raw hex in USWDS projects
color: color('primary');
background: color('base-lightest');
border-color: color('error-dark');
```

### Typography

```scss
// Font size tokens
font-size: size('body', 'lg');
font-size: size('heading', '2xl');

// Family tokens
font-family: family('sans');
font-family: family('serif');
```

## Key Settings Tokens

Configure in your `@use "uswds-core" with (...)` block:

```scss
// Typography
$theme-typeface-tokens: (...)
$theme-font-type-sans: 'public-sans'
$theme-font-type-serif: 'merriweather'
$theme-body-font-size: 'sm'
$theme-h1-font-size: '2xl'

// Color
$theme-color-primary: 'blue-60v'
$theme-color-secondary: 'red-50v'
$theme-color-accent-cool: 'cyan-30v'
$theme-color-base-ink: 'gray-90'

// Spacing
$theme-site-max-width: 'widescreen'
$theme-column-gap: 4

// Grid
$theme-grid-responsive-prefix: ''

// Focus
$theme-focus-color: 'gold-30v'
$theme-focus-offset: 0
$theme-focus-style: 'outline'
$theme-focus-width: 4
```

## Component Usage

### Header

```html
<header class="usa-header usa-header--basic" role="banner">
  <div class="usa-nav-container">
    <div class="usa-navbar">
      <div class="usa-logo">
        <em class="usa-logo__text"><a href="/" title="Agency Name">Agency Name</a></em>
      </div>
      <button type="button" class="usa-menu-btn">Menu</button>
    </div>
    <nav aria-label="Primary navigation" class="usa-nav">
      <button type="button" class="usa-nav__close"><img src="/img/usa-icons/close.svg" role="img" alt="Close"></button>
      <ul class="usa-nav__primary usa-accordion">
        <li class="usa-nav__primary-item">
          <a href="/section-1" class="usa-nav__link"><span>Section 1</span></a>
        </li>
      </ul>
    </nav>
  </div>
</header>
```

### Alert

```html
<!-- Informational -->
<div class="usa-alert usa-alert--info" role="region" aria-label="Information alert">
  <div class="usa-alert__body">
    <h4 class="usa-alert__heading">Informational status</h4>
    <p class="usa-alert__text">Your form has been submitted.</p>
  </div>
</div>

<!-- Error -->
<div class="usa-alert usa-alert--error" role="alert">
  <div class="usa-alert__body">
    <h4 class="usa-alert__heading">Error status</h4>
    <p class="usa-alert__text">There were errors in your submission.</p>
  </div>
</div>
```

### Form

```html
<form class="usa-form usa-form--large">
  <fieldset class="usa-fieldset">
    <div class="usa-form-group">
      <label class="usa-label" for="email">Email address</label>
      <span class="usa-hint" id="email-hint">Enter your official .gov email</span>
      <input
        class="usa-input"
        id="email"
        name="email"
        type="email"
        aria-describedby="email-hint"
        autocomplete="email"
      >
    </div>
    <div class="usa-form-group usa-form-group--error">
      <label class="usa-label usa-label--error" for="phone">Phone</label>
      <span class="usa-error-message" id="phone-error" role="alert">Enter a 10-digit phone number</span>
      <input class="usa-input usa-input--error" id="phone" name="phone" type="tel" aria-describedby="phone-error">
    </div>
  </fieldset>
  <button type="submit" class="usa-button">Submit</button>
</form>
```

### Card

```html
<ul class="usa-card-group">
  <li class="usa-card tablet:grid-col-4">
    <div class="usa-card__container">
      <div class="usa-card__header">
        <h2 class="usa-card__heading">Card heading</h2>
      </div>
      <div class="usa-card__media">
        <div class="usa-card__img">
          <img src="/img/placeholder.jpg" alt="Descriptive alt text">
        </div>
      </div>
      <div class="usa-card__body">
        <p>Card body content.</p>
      </div>
      <div class="usa-card__footer">
        <a href="/detail" class="usa-button">Read more</a>
      </div>
    </div>
  </li>
</ul>
```

## Grid System

USWDS uses a 12-column grid based on CSS Grid.

```html
<div class="grid-container">
  <div class="grid-row grid-gap">
    <div class="tablet:grid-col-8">Main content</div>
    <div class="tablet:grid-col-4">Sidebar</div>
  </div>
</div>
```

Responsive prefixes: `mobile-lg:`, `tablet:`, `tablet-lg:`, `desktop:`, `desktop-lg:`, `widescreen:`

## JavaScript Initialization

USWDS JS must be initialized after the DOM is ready:

```js
import uswds from '@uswds/uswds/js';
// USWDS auto-initializes on DOMContentLoaded when imported this way
```

Or with the prebuilt JS:

```html
<script src="/js/uswds-init.min.js"></script><!-- In <head> — prevents flash -->
<script src="/js/uswds.min.js" defer></script><!-- Before </body> -->
```

## Customization Rules

- Customize through settings tokens — not by overriding `.usa-` classes directly
- If you must override, scope tightly and document why
- Do not rename or remove ARIA attributes on USWDS components — they are accessibility-critical
- Test custom components against the same keyboard and screen reader checklist as native USWDS components

## Accessibility Notes

USWDS components are built to WCAG 2.1 AA. When extending:
- Preserve `role`, `aria-*`, and `tabindex` attributes
- Do not hide content with `visibility: hidden` if it must be accessible to screen readers
- Color customizations must maintain the 4.5:1 contrast ratio for text

## 21st Century IDEA Compliance Checklist

- [ ] Mobile-responsive design
- [ ] Accessible to people with disabilities (Section 508 / WCAG 2.1 AA)
- [ ] Consistent with USWDS
- [ ] Searchable content
- [ ] Contains required links (privacy policy, accessibility statement)
- [ ] Written in plain language
- [ ] Available in multiple formats where applicable
