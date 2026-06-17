---
name: vads
description: Implements and customizes the VA Design System (VADS). Use when building VA.gov properties, using VADS web components or utility classes, or ensuring compliance with VA accessibility and design standards. Use when the user mentions VADS, VA Design System, va-alert, va-button, va-accordion, VA.gov, or veteran-facing services.
---

# VADS — VA Design System

## Overview

VADS is the Department of Veterans Affairs design system, built on top of USWDS with VA-specific extensions. It ships as native Web Components (`<va-*>`) plus a utility CSS class layer. VA.gov and all veteran-facing digital services use VADS. Do not bypass VADS components with custom equivalents when an official component exists.

**Docs:** https://design.va.gov  
**Component storybook:** https://design.va.gov/storybook/  
**Source:** https://github.com/department-of-veterans-affairs/component-library  
**npm:** `@department-of-veterans-affairs/component-library`

## When to Use

- Building or maintaining VA.gov or affiliated veteran-facing properties
- Implementing VADS Web Components
- Using VADS utility classes and Formation tokens
- Auditing a VA property for design system compliance
- Working with the VA's Drupal CMS (CMS team content pages use VADS)

## Installation

```bash
npm install @department-of-veterans-affairs/component-library
npm install @department-of-veterans-affairs/css-library
```

Register all components (global registration):

```js
import { defineCustomElements } from '@department-of-veterans-affairs/component-library/loader';
defineCustomElements();
```

Or register individual components:

```js
import { VaAlert } from '@department-of-veterans-affairs/component-library/dist/components/va-alert';
customElements.define('va-alert', VaAlert);
```

Include CSS:

```html
<link rel="stylesheet" href="https://unpkg.com/@department-of-veterans-affairs/css-library/dist/tokens/css/variables.css">
<link rel="stylesheet" href="https://unpkg.com/@department-of-veterans-affairs/css-library/dist/stylesheets/utilities.css">
```

## Web Components

VADS components are standard Web Components. They use Shadow DOM and support both attribute and property APIs.

### va-alert

```html
<!-- Status variants: info, success, warning, error, continue -->
<va-alert
  status="info"
  headline="Application received"
  visible
>
  <p slot="content">We'll contact you within 30 days.</p>
</va-alert>

<va-alert status="error" headline="Something went wrong" visible>
  <p slot="content">Please try again or call 1-800-827-1000.</p>
</va-alert>
```

### va-button

```html
<va-button text="Continue" />
<va-button text="Back" secondary />
<va-button text="Cancel" label="Cancel and return to home" />
```

### va-accordion

```html
<va-accordion>
  <va-accordion-item header="What documents do I need?">
    <p>You will need your DD214 and proof of identity.</p>
  </va-accordion-item>
  <va-accordion-item header="How long will it take?">
    <p>Processing typically takes 60 to 90 days.</p>
  </va-accordion-item>
</va-accordion>
```

### va-text-input

```html
<va-text-input
  label="Your full name"
  name="full-name"
  required
  hint="As it appears on your government-issued ID"
/>

<!-- With error -->
<va-text-input
  label="Date of birth"
  name="dob"
  error="Enter a valid date of birth"
  value=""
/>
```

### va-select

```html
<va-select label="Branch of service" name="branch" required>
  <option value="">- Select -</option>
  <option value="army">Army</option>
  <option value="navy">Navy</option>
  <option value="air-force">Air Force</option>
  <option value="marines">Marine Corps</option>
  <option value="coast-guard">Coast Guard</option>
  <option value="space-force">Space Force</option>
</va-select>
```

### va-checkbox-group

```html
<va-checkbox-group
  label="Which benefits are you applying for?"
  name="benefits"
  hint="Select all that apply"
>
  <va-checkbox label="Disability compensation" name="disability" />
  <va-checkbox label="Education benefits" name="education" />
  <va-checkbox label="Home loan guaranty" name="home-loan" />
</va-checkbox-group>
```

### va-pagination

```html
<va-pagination
  page="2"
  pages="10"
  unbounded
/>
```

### va-breadcrumbs

```html
<va-breadcrumbs>
  <a href="/" slot="home">Home</a>
  <a href="/disability/">Disability</a>
  <a href="/disability/eligibility/">Eligibility</a>
</va-breadcrumbs>
```

### va-loading-indicator

```html
<va-loading-indicator
  label="Loading your claims"
  message="Please wait while we retrieve your information"
/>
```

## Listening to Component Events

VADS components fire standard Custom Events. Event names follow the pattern `component:action`:

```js
document.querySelector('va-text-input').addEventListener('input', e => {
  console.log(e.target.value);
});

document.querySelector('va-select').addEventListener('vaSelect', e => {
  console.log(e.detail.value);
});

document.querySelector('va-alert').addEventListener('closeEvent', () => {
  // Handle dismiss
});
```

Check the Storybook "Events" tab for each component's emitted events.

## Utility Classes

VADS ships a utility class layer similar to Tailwind, built on USWDS tokens:

```html
<!-- Spacing -->
<div class="vads-u-margin-bottom--4 vads-u-padding--2">

<!-- Typography -->
<p class="vads-u-font-size--lg vads-u-font-weight--bold">

<!-- Color -->
<p class="vads-u-color--primary vads-u-background-color--gray-lightest">

<!-- Display / layout -->
<div class="vads-u-display--flex vads-u-align-items--center vads-u-justify-content--space-between">
```

Naming pattern: `vads-u-{property}--{value}`

## Design Tokens

VADS tokens extend USWDS. Access via CSS custom properties:

```css
color: var(--vads-color-action);
background: var(--vads-color-feedback-error-lighter);
font-size: var(--vads-font-size-base);
```

## Accessibility in VADS

VADS Web Components handle most accessibility automatically (ARIA, focus management, keyboard support). Your responsibilities:

- Always provide `label` attributes on form components — never omit them
- Use `hint` for secondary instructions, not `placeholder`
- Use `error` attribute for validation messages — do not add custom error markup next to VADS inputs
- Do not use `display: none` or `visibility: hidden` to suppress components — use the `visible` attribute or conditional rendering
- Test with NVDA + Firefox and JAWS + Chrome — the same bar as any Section 508 work

## Content and Plain Language

VA.gov has strict plain language requirements:

- Reading level: 8th grade or below (Hemingway App is a quick check)
- Use "you" and "we" — not "the veteran" or "the department"
- Spell out acronyms on first use
- Lead with what the veteran can do, not eligibility restrictions
- Use active voice

## Compliance Checklist

- [ ] All VADS components used where an official component exists (no custom duplicates)
- [ ] All form inputs have `label` attributes
- [ ] Error messages use `error` attribute on the component, not adjacent custom HTML
- [ ] Page uses `va-breadcrumbs` where navigation context is needed
- [ ] Loading states use `va-loading-indicator`
- [ ] Alerts use `va-alert` with appropriate `status` variant
- [ ] No VADS Shadow DOM styles overridden via CSS custom property hacks
- [ ] Screen reader tested with NVDA + Firefox
- [ ] Plain language review completed
- [ ] Mobile responsive on 320px minimum viewport
