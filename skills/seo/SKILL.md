---
name: seo
description: Audits and improves technical and on-page SEO. Use when optimizing pages for search engines, implementing structured data, fixing crawlability issues, auditing meta tags, or improving Core Web Vitals for ranking. Use when the user mentions SEO, meta tags, structured data, schema.org, canonical URLs, sitemaps, robots.txt, or organic search.
---

# SEO

## Overview

Modern SEO has two layers: technical correctness (crawlable, indexable, fast) and content signals (relevance, authority, freshness). Fix technical issues first — they block everything else. Content signals only help when the foundation is solid.

## When to Use

- Implementing or auditing meta tags and Open Graph
- Adding structured data (JSON-LD)
- Fixing crawlability or indexability issues
- Improving Core Web Vitals for ranking signals
- Setting up or auditing sitemaps and robots.txt
- Auditing redirects and canonical URLs

## Technical Foundation

### Essential meta tags

```html
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">

  <title>Page Title — Site Name</title><!-- 50–60 chars, keyword near front -->
  <meta name="description" content="150–160 char description. Make it accurate and enticing — this is often the snippet.">

  <!-- Canonical — prevent duplicate content -->
  <link rel="canonical" href="https://www.example.gov/exact-url/">

  <!-- Robots — only needed to restrict; default is index, follow -->
  <!-- <meta name="robots" content="noindex, nofollow"> -->
</head>
```

Title rules:
- 50–60 characters (Google truncates longer titles)
- Primary keyword near the front
- Brand at the end, separated by a dash or pipe
- Every page must have a unique title

Description rules:
- 150–160 characters
- Does not directly affect ranking, but affects click-through rate
- Must be unique per page — not auto-generated boilerplate

### Open Graph (social sharing)

```html
<meta property="og:title" content="Page Title">
<meta property="og:description" content="Description for social cards.">
<meta property="og:url" content="https://www.example.gov/page/">
<meta property="og:type" content="website"><!-- or "article" for blog posts -->
<meta property="og:image" content="https://www.example.gov/img/og-image.jpg">
<meta property="og:image:width" content="1200">
<meta property="og:image:height" content="630">
<meta property="og:site_name" content="Site Name">

<!-- Twitter Card -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:site" content="@handle">
```

OG image: 1200×630px minimum. PNG or JPG. Do not include text that will be cut off in previews.

### Canonical URLs

```html
<!-- Self-referencing canonical on every page -->
<link rel="canonical" href="https://www.example.gov/page/">

<!-- On paginated pages — canonical to the first page only if content is the same -->
<!-- Better: use rel="next" and rel="prev" instead -->
<link rel="prev" href="https://www.example.gov/blog/?page=1">
<link rel="next" href="https://www.example.gov/blog/?page=3">
```

Rules:
- Always use HTTPS in canonical URLs
- Always use the trailing slash consistently (pick one, canonical it everywhere)
- Never canonicalize to a redirecting URL

### robots.txt

```
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /search?
Disallow: /user/

Sitemap: https://www.example.gov/sitemap.xml
```

Rules:
- `Disallow` blocks crawling, not indexing — use `noindex` meta tags or X-Robots-Tag headers for indexing control
- Do not disallow your CSS and JS — Googlebot needs them to render pages
- Sitemap URL should be absolute

### XML Sitemap

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://www.example.gov/</loc>
    <lastmod>2025-06-01</lastmod>
    <changefreq>weekly</changefreq>
    <priority>1.0</priority>
  </url>
</urlset>
```

Rules:
- Include only indexable pages (no `noindex`, no 4xx/5xx, no canonicalized-away URLs)
- Submit via Google Search Console
- For large sites, use a sitemap index file pointing to multiple sitemaps (50k URL / 50MB per sitemap limit)

## Structured Data (JSON-LD)

Place `<script type="application/ld+json">` in `<head>` or at end of `<body>`. JSON-LD is preferred over Microdata.

### Article

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "Page Title",
  "datePublished": "2025-06-01T00:00:00Z",
  "dateModified": "2025-06-15T00:00:00Z",
  "author": {
    "@type": "Person",
    "name": "Author Name"
  },
  "publisher": {
    "@type": "Organization",
    "name": "Agency Name",
    "logo": {
      "@type": "ImageObject",
      "url": "https://www.example.gov/img/logo.png"
    }
  },
  "image": "https://www.example.gov/img/article-image.jpg",
  "description": "Short description of the article."
}
</script>
```

### Breadcrumb

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    { "@type": "ListItem", "position": 1, "name": "Home", "item": "https://www.example.gov/" },
    { "@type": "ListItem", "position": 2, "name": "Benefits", "item": "https://www.example.gov/benefits/" },
    { "@type": "ListItem", "position": 3, "name": "Disability", "item": "https://www.example.gov/benefits/disability/" }
  ]
}
</script>
```

### FAQ

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What documents do I need?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "You will need your DD214 and proof of identity."
      }
    }
  ]
}
</script>
```

Validate at: https://search.google.com/test/rich-results

## Core Web Vitals (Ranking Signals)

| Metric | Target | Common Cause of Failure |
|---|---|---|
| LCP (Largest Contentful Paint) | < 2.5s | Unoptimized hero image, slow server |
| INP (Interaction to Next Paint) | < 200ms | Long JS tasks blocking main thread |
| CLS (Cumulative Layout Shift) | < 0.1 | Images without dimensions, late-loading ads/fonts |

Quick wins:
- Set `width` and `height` on all `<img>` elements
- Preload the LCP image: `<link rel="preload" as="image" href="hero.jpg">`
- Use `font-display: swap` for web fonts
- Defer non-critical JS: `<script defer>`
- Serve images as WebP/AVIF
- Use a CDN for static assets

## Heading Structure for SEO

- One `<h1>` per page — matches or is close to the `<title>` tag
- Do not skip levels
- Headings convey page structure to crawlers — treat them like an outline
- Keywords in headings help, but write for humans first

## Redirects

- 301 (permanent) for permanently moved content — passes link equity
- 302 (temporary) for genuinely temporary moves — does not pass link equity
- Avoid redirect chains (A → B → C) — collapse to A → C
- Update internal links to point directly to the final destination
- Audit redirects with Screaming Frog or a similar crawler

## Audit Checklist

- [ ] Every page has a unique `<title>` (50–60 chars)
- [ ] Every page has a unique `<meta name="description">` (150–160 chars)
- [ ] Self-referencing canonical on every page
- [ ] OG tags present and validated (https://developers.facebook.com/tools/debug/)
- [ ] robots.txt accessible at root, no critical paths blocked
- [ ] XML sitemap submitted to Search Console
- [ ] Structured data validated with Rich Results Test
- [ ] Core Web Vitals passing (check Search Console or PageSpeed Insights)
- [ ] No broken internal links (4xx)
- [ ] No redirect chains
- [ ] HTTPS everywhere, no mixed content
- [ ] `lang` attribute on `<html>` element
- [ ] Heading hierarchy correct (one h1, no skipped levels)
