---
name: accessibility-508-wcag
description: Audits and implements accessible web interfaces. Use when building or reviewing any web UI, when checking compliance with Section 508, WCAG 2.1/2.2 AA, or when integrating with USWDS or VADS. Use when the user mentions accessibility, a11y, screen readers, ARIA, axe-core, keyboard navigation, or government/federal web work.
---

# Accessibility: 508 / WCAG

## Overview

Accessibility is a legal requirement on federal properties and a baseline quality standard everywhere else. Build to WCAG 2.2 AA minimum. Section 508 (29 U.S.C. § 794d) incorporates WCAG 2.1 AA by reference — satisfying WCAG 2.2 AA covers both. Do not treat accessibility as a phase; retrofit costs 10x more than building it in.

## When to Use

- Building or reviewing any HTML/CSS/JS interface
- Auditing an existing page or component
- Implementing or extending USWDS or VADS components
- Fixing failures surfaced by axe-core, NVDA, JAWS, VoiceOver, or Lighthouse
- Preparing for a Section 508 compliance review

## Audit Process

Run these in order — automated tools catch ~30% of issues; the rest require manual checks.

### 1. Automated scan

```bash
# axe-core via CLI
npx axe-cli https://example.com --tags wcag2a,wcag2aa,wcag21aa,wcag22aa

# Or in browser: install axe DevTools extension, run on each page state
```

Capture all violations. Do not dismiss "needs review" items — they are candidates, not false positives.

### 2. Keyboard navigation

Tab through every interactive element in order:
- [ ] Focus is always visible (WCAG 2.4.7 — do not remove `outline` without a visible replacement)
- [ ] Tab order matches visual reading order
- [ ] All interactive elements reachable by keyboard alone
- [ ] No keyboard traps (WCAG 2.1.2)
- [ ] Modal dialogs trap focus within and restore on close
- [ ] Escape closes dialogs, menus, tooltips

### 3. Screen reader testing

Test with at least two combinations:

| SR | Browser | Priority |
|---|---|---|
| NVDA + Firefox | Windows | High (most common on govt desktops) |
| JAWS + Chrome | Windows | High (common in agencies) |
| VoiceOver + Safari | macOS/iOS | Medium |

Check each page state, not just the initial load. Dynamic content must be announced.

### 4. Color and contrast

- Text: 4.5:1 minimum (3:1 for large text ≥ 18pt or 14pt bold)
- UI components and focus indicators: 3:1 against adjacent colors
- Never use color alone to convey meaning (WCAG 1.4.1)

Tools: Colour Contrast Analyser, browser DevTools contrast checker.

### 5. Images and media

- All `<img>` elements have `alt` text (empty `alt=""` for decorative images)
- Complex images (charts, diagrams) have long descriptions
- Video has captions; audio has transcripts
- No content flashes more than 3 times per second (WCAG 2.3.1)

## Implementation Patterns

### Semantic HTML first

Use the right element before reaching for ARIA. ARIA only supplements — it does not replace semantics.

```html
<!-- Correct -->
<button type="button">Submit</button>
<nav aria-label="Primary">...</nav>
<main>...</main>

<!-- Wrong — ARIA on a div when a native element exists -->
<div role="button" tabindex="0">Submit</div>
```

### Focus management

```js
// After dynamic content loads or a dialog opens, move focus explicitly
dialog.addEventListener('open', () => {
  const firstFocusable = dialog.querySelector('button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])');
  firstFocusable?.focus();
});

// On dialog close, restore focus to the trigger
closeBtn.addEventListener('click', () => {
  dialog.close();
  triggerEl.focus();
});
```

### Live regions for dynamic content

```html
<!-- Status messages (non-interrupting) -->
<div role="status" aria-live="polite" aria-atomic="true"></div>

<!-- Alerts (interrupting) -->
<div role="alert" aria-live="assertive"></div>
```

Inject text into the live region after a brief delay (50ms) so the SR picks up the DOM change.

### Form labeling

```html
<!-- Always associate labels explicitly -->
<label for="email">Email address</label>
<input id="email" type="email" autocomplete="email" aria-describedby="email-hint email-error">
<p id="email-hint">We'll only use this to contact you about your claim.</p>
<p id="email-error" role="alert" hidden>Enter a valid email address.</p>
```

Never use `placeholder` as a label substitute — it disappears on input and has insufficient contrast.

### Skip navigation

Every page needs a skip link as the first focusable element:

```html
<a class="skip-link" href="#main-content">Skip to main content</a>
```

```css
.skip-link {
  position: absolute;
  transform: translateY(-100%);
}
.skip-link:focus {
  transform: translateY(0);
}
```

### Headings

One `<h1>` per page. Heading levels must not skip (h1 → h2 → h3, never h1 → h3). Headings are navigational landmarks for SR users — every major section needs one.

### Tables

```html
<table>
  <caption>FY2025 Budget Summary</caption>
  <thead>
    <tr>
      <th scope="col">Category</th>
      <th scope="col">Amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">Personnel</th>
      <td>$1,200,000</td>
    </tr>
  </tbody>
</table>
```

Never use tables for layout. Always use `scope` on header cells.

## Common WCAG Failures

| Failure | Criterion | Fix |
|---|---|---|
| Images missing alt | 1.1.1 | Add meaningful alt or `alt=""` for decorative |
| Color-only error state | 1.4.1 | Add icon + text alongside color |
| Low contrast text | 1.4.3 | Adjust colors to meet ratio |
| No skip link | 2.4.1 | Add skip nav as first focusable |
| Focus not visible | 2.4.7 | Restore or enhance browser focus outline |
| Input no label | 1.3.1 | Associate `<label>` or `aria-label` |
| Modal no focus trap | 2.1.2 | Implement focus trap on open |
| Dynamic content not announced | 4.1.3 | Use live regions |
| Link text "click here" | 2.4.6 | Rewrite to describe destination |
| Auto-playing media | 1.4.2 | Provide pause/stop control |

## Section 508 Specifics

- 508 applies to all federal agencies and contractors developing for federal use
- Incorporates WCAG 2.1 Level AA via the 2018 refresh
- Electronic documents (PDFs, Word, Excel) also fall under 508 — not just web
- VPAT (Voluntary Product Accessibility Template) documents conformance — required for procurement

## Testing Checklist

- [ ] Automated axe scan: zero violations
- [ ] Keyboard-only navigation complete
- [ ] NVDA + Firefox: all content readable, dynamic updates announced
- [ ] Color contrast ratios pass (text and UI components)
- [ ] All images have appropriate alt text
- [ ] All form inputs have associated labels
- [ ] Skip link present and functional
- [ ] Heading hierarchy correct
- [ ] Focus management on modals/dialogs
- [ ] No keyboard traps
- [ ] Page has `<title>` that describes current state
- [ ] Language attribute set (`<html lang="en">`)
