---
name: vanilla-js
description: Builds browser functionality with native JavaScript and Web APIs. Use when implementing interactivity, DOM manipulation, events, fetch, or any browser-side logic without a framework. Use when the user asks for vanilla JS, native browser APIs, progressive enhancement, or ES modules. Use when a framework would be overkill.
---

# Vanilla JS

## Overview

Reach for the platform before reaching for a library. Modern browsers expose the DOM, Fetch, Web Components, Intersection Observer, and much more natively — and native code has zero bundle cost, no dependency churn, and no upgrade path. The bar for adding a library should be: "Does this problem genuinely exceed what the browser gives me?"

## When to Use

- Adding interactivity to a page without a framework
- Building a reusable utility with no app-specific dependencies
- Enhancing server-rendered HTML (progressive enhancement)
- Performance-sensitive code where bundle size matters
- USWDS / VADS / Drupal front-end work (no React in scope)

## Module Pattern

Use ES modules natively in the browser or with a minimal bundler (esbuild, Rollup). No CommonJS.

```js
// utils/dom.js
export const qs = (sel, ctx = document) => ctx.querySelector(sel);
export const qsa = (sel, ctx = document) => [...ctx.querySelectorAll(sel)];
export const on = (el, event, fn, opts) => el.addEventListener(event, fn, opts);

// main.js
import { qs, qsa, on } from './utils/dom.js';
```

## DOM Manipulation

```js
// Create elements with attributes in one pass
const createEl = (tag, attrs = {}, children = []) => {
  const el = Object.assign(document.createElement(tag), attrs);
  el.append(...children);
  return el;
};

// Example
const btn = createEl('button', { type: 'button', className: 'usa-button' }, ['Submit']);

// Prefer insertAdjacentHTML for larger HTML strings (faster than innerHTML assignment in a loop)
container.insertAdjacentHTML('beforeend', `<li class="item">${escapeHtml(label)}</li>`);
```

Always escape user-generated content before inserting into the DOM:

```js
const escapeHtml = str =>
  str.replace(/[&<>"']/g, c => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#39;' }[c]));
```

## Events

Delegate events to a stable ancestor when handling dynamic lists:

```js
document.querySelector('.list').addEventListener('click', e => {
  const item = e.target.closest('[data-action]');
  if (!item) return;
  handleAction(item.dataset.action, item);
});
```

Debounce high-frequency events:

```js
const debounce = (fn, ms = 200) => {
  let t;
  return (...args) => {
    clearTimeout(t);
    t = setTimeout(() => fn(...args), ms);
  };
};

window.addEventListener('resize', debounce(onResize, 150));
```

Use `{ passive: true }` on scroll/touch listeners that never call `preventDefault`:

```js
window.addEventListener('scroll', onScroll, { passive: true });
```

## Fetch and Data

```js
// Generic JSON fetch with error handling
const fetchJSON = async (url, options = {}) => {
  const res = await fetch(url, {
    headers: { 'Content-Type': 'application/json' },
    ...options,
  });
  if (!res.ok) throw new Error(`HTTP ${res.status}: ${res.statusText}`);
  return res.json();
};

// POST with JSON body
const data = await fetchJSON('/api/submit', {
  method: 'POST',
  body: JSON.stringify(payload),
});
```

Never concatenate user input into URLs — use `URLSearchParams`:

```js
const params = new URLSearchParams({ q: userInput, page: 1 });
const url = `/api/search?${params}`;
```

## Observers

### IntersectionObserver (lazy load, scroll-triggered)

```js
const observer = new IntersectionObserver(
  entries => entries.forEach(e => { if (e.isIntersecting) loadContent(e.target); }),
  { rootMargin: '200px' }
);

document.querySelectorAll('[data-lazy]').forEach(el => observer.observe(el));
```

### MutationObserver (react to DOM changes)

```js
const mo = new MutationObserver(mutations => {
  for (const m of mutations) {
    m.addedNodes.forEach(node => {
      if (node.nodeType === 1 && node.matches('[data-component]')) initComponent(node);
    });
  }
});

mo.observe(document.body, { childList: true, subtree: true });
```

## Progressive Enhancement

Start with working HTML. JS enhances it — does not replace it.

```html
<!-- Works without JS -->
<form method="post" action="/search">
  <input name="q" type="search">
  <button>Search</button>
</form>
```

```js
// Enhance with fetch when JS is available
const form = document.querySelector('form[action="/search"]');
form?.addEventListener('submit', async e => {
  e.preventDefault();
  const q = new FormData(form).get('q');
  const results = await fetchJSON(`/api/search?${new URLSearchParams({ q })}`);
  renderResults(results);
});
```

## State Management

For simple state, a plain object with explicit update functions is enough:

```js
const state = { items: [], loading: false, error: null };

const update = patch => {
  Object.assign(state, patch);
  render();
};

const render = () => {
  // Derive DOM from state
};
```

Only reach for a state library (Zustand, Nanostores, etc.) when state is shared across many distant components and prop-drilling or custom events become unwieldy.

## Custom Events for Cross-Component Communication

```js
// Dispatch
el.dispatchEvent(new CustomEvent('itemSelected', {
  bubbles: true,
  composed: true, // cross shadow DOM
  detail: { id: item.id, label: item.label },
}));

// Listen anywhere above in the DOM
document.addEventListener('itemSelected', e => {
  console.log(e.detail);
});
```

## Patterns to Avoid

| Pattern | Problem | Replacement |
|---|---|---|
| `document.write()` | Blocks parser, XSS risk | `insertAdjacentHTML` |
| `innerHTML = userInput` | XSS | `textContent` or escape first |
| `var` | Function-scoped, hoisted | `const` / `let` |
| Polling with `setInterval` | Wastes CPU | `MutationObserver`, `IntersectionObserver`, `EventSource` |
| jQuery `$.ajax` | Unnecessary dep | `fetch` |
| Inline event attributes `onclick=""` | Couples HTML and JS, CSP risk | `addEventListener` |
| Global variables | Collisions, implicit coupling | ES module scope |

## Checklist

- [ ] No framework added when native APIs suffice
- [ ] All user input escaped before DOM insertion
- [ ] Event listeners delegated to stable ancestors where appropriate
- [ ] High-frequency events debounced or throttled
- [ ] `fetch` calls handle non-2xx responses explicitly
- [ ] No `var`
- [ ] ES modules used for code organization
- [ ] Progressive enhancement: HTML works before JS executes
