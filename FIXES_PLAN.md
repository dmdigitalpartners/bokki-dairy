# Млечко — Implementation Plan

## Context

Single-page website at `index.html` (~1,370 lines). Pure HTML + inline CSS + inline JS. No build step.  
Dev server: `node serve.mjs` → `http://localhost:3000`  
Screenshots: `node screenshot.mjs http://localhost:3000 <label>`

**Already completed (do not redo):**
- `serve.mjs`: `decodeURIComponent` fix + `.mp4` MIME type added
- All 6 category/featured images swapped to `Enhanced images/` folder

After every pass: start the dev server if not running, take a screenshot, read it, verify visually before marking done.

---

## Pass 1 — Critical (Accessibility + SEO + Performance)

### 1.1 Remove wasted preload + add video poster

**File:** `index.html`

**Step A** — Delete line ~10:
```html
<link rel="preload" as="image" href="images/hero-header.png" fetchpriority="high">
```

**Step B** — On the `<video>` tag at line ~822, add `poster` attribute:
```html
<video class="hero-video" autoplay muted loop playsinline aria-hidden="true" poster="images/hero-header.png">
```

---

### 1.2 Add `<main>` landmark

**File:** `index.html`

Find the closing `</header>` tag (around line 816). Immediately after it, add:
```html
<main id="main-content">
```

Find the opening `<footer` tag (around line 1176). Immediately before it, add:
```html
</main>
```

---

### 1.3 Fix multiple `<h1>` tags — add `aria-hidden` to inactive hero panels

**File:** `index.html` JS section (around line 1270–1290)

Find the hero slideshow JS. There will be a function that activates panels — look for where `.active` class is added/removed. In that function, after toggling `.active`, add logic to set `aria-hidden`:

```js
// After the existing panel activation logic, add:
panels.forEach((p, i) => {
  p.setAttribute('aria-hidden', i !== index ? 'true' : 'false');
});
```

Also on initial load, set `aria-hidden="true"` on panels 1 and 2 (index 1 and 2) in the HTML directly:

**Panel 2** (line ~833): add `aria-hidden="true"` to its wrapping `<div class="hero-panel">`  
**Panel 3** (line ~842): add `aria-hidden="true"` to its wrapping `<div class="hero-panel">`  
**Panel 1** (line ~823): add `aria-hidden="false"` to its wrapping `<div class="hero-panel active">`

---

### 1.4 Fix color contrast — sage-colored buttons

**File:** `index.html` `<style>` block

The issue: white text (#ffffff) on sage (#7c9a7e) = 3.09:1 contrast ratio. Need ≥ 4.5:1.

Find `.nav-cta` CSS rule and `.btn-primary` CSS rule. Change their `background` / `background-color` value from `var(--color-sage)` or `#7c9a7e` to `#4d7a50`.

Do NOT change `--color-sage` in `:root` — that variable is used for text, checkmarks, eyebrows (which pass contrast). Only change the button background-color declarations.

Verify: after change, "СВЪРЖЕТЕ СЕ" nav button and both hero CTA buttons should be slightly darker green.

---

### 1.5 Fix color contrast — "2010" badge text

**File:** `index.html` `<style>` block

Find `.collage-badge-label` CSS rule. Change `color` to `#2C3E50`.

The badge has two label spans ("От" and "Година") displayed above/below the "2010" number. These currently render near-white on gold (1.94:1 ratio — fails all WCAG levels).

---

### 1.6 Wrap WhatsApp FAB in a landmark

**File:** `index.html` around line 1237

Find the `.wa-fab` anchor element. Wrap it:
```html
<aside aria-label="Контакт чрез WhatsApp">
  <a href="https://wa.me/359878232365" class="wa-fab" ...>
    ...
  </a>
</aside>
```

---

## Pass 2 — Moderate (UX + SEO + Copy)

### 2.1 Add skip-to-content link

**File:** `index.html`

**Step A** — Add as the very first child of `<body>` (before `<header>`):
```html
<a href="#main-content" class="skip-link">Прескочи към съдържанието</a>
```

**Step B** — Add CSS in the `<style>` block (near the top, after `:root`):
```css
.skip-link {
  position: absolute;
  left: -9999px;
  top: 1rem;
  z-index: 9999;
  padding: 12px 20px;
  background: var(--color-sage);
  color: #fff;
  font-family: var(--font-sans);
  font-size: 0.9rem;
  border-radius: 4px;
  text-decoration: none;
}
.skip-link:focus {
  left: 1rem;
}
```

---

### 2.2 Fix mobile nav overlay — body bleeds through

**File:** `index.html` `<style>` block

Find the `.mobile-nav` or `.nav-mobile` CSS rule (look for the full-screen mobile nav overlay styles, somewhere around the navigation section of the CSS, lines 105–220).

Find the `background` or `background-color` property of the mobile nav overlay container. Increase its alpha to at least `0.97`. Example:
```css
background: rgba(10, 10, 30, 0.97);
```

Screenshot the mobile nav open state to verify: `node screenshot.mjs http://localhost:3000 mobile-nav-check` then read the PNG.  
*(The puppeteer screenshot won't auto-open the nav — verify by resizing browser manually, or write a quick puppeteer snippet to click the hamburger and screenshot.)*

---

### 2.3 Add hero carousel dot indicators

**File:** `index.html`

**Step A** — In the hero section HTML (around line 853, after the last `.hero-panel` div and before the wave divider), add:
```html
<div class="hero-dots" role="tablist" aria-label="Слайдове на героя">
  <button class="hero-dot active" role="tab" aria-selected="true" aria-label="Слайд 1"></button>
  <button class="hero-dot" role="tab" aria-selected="false" aria-label="Слайд 2"></button>
  <button class="hero-dot" role="tab" aria-selected="false" aria-label="Слайд 3"></button>
</div>
```

**Step B** — Add CSS:
```css
.hero-dots {
  position: absolute;
  bottom: 88px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 10px;
  z-index: 10;
}
.hero-dot {
  width: 8px; height: 8px;
  border-radius: 50%;
  border: none;
  background: rgba(255,255,255,0.35);
  cursor: pointer;
  padding: 0;
  transition: background 0.3s, transform 0.3s;
}
.hero-dot.active {
  background: #fff;
  transform: scale(1.3);
}
```

**Step C** — In the hero JS (near the panel activation logic), add dot sync:
```js
const dots = document.querySelectorAll('.hero-dot');
// Inside the panel activation function, after activating the panel:
dots.forEach((d, i) => {
  d.classList.toggle('active', i === index);
  d.setAttribute('aria-selected', i === index ? 'true' : 'false');
});
// Add click handlers after dots are found:
dots.forEach((dot, i) => dot.addEventListener('click', () => activatePanel(i)));
```
*(Replace `activatePanel(i)` with whatever the actual panel-switch function is named in the existing JS.)*

---

### 2.4 Add Open Graph + Twitter Card meta tags

**File:** `index.html` `<head>`

Add after the existing `<meta name="description">` line (~line 8):
```html
<meta property="og:title" content="Млечко — Специализиран магазин за млечни продукти · Пловдив">
<meta property="og:description" content="Пресни млечни продукти, яйца и маслини. Доставка всяка сутрин директно от производителите.">
<meta property="og:image" content="https://mlechko.bg/images/Mlechko-Logo.png">
<meta property="og:type" content="business.business">
<meta property="og:locale" content="bg_BG">
<meta property="og:url" content="https://mlechko.bg">
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Млечко — Специализиран магазин · Пловдив">
<meta name="twitter:description" content="Пресни млечни продукти, яйца и маслини. Доставка всяка сутрин.">
<link rel="canonical" href="https://mlechko.bg">
```

---

### 2.5 Add JSON-LD LocalBusiness schema

**File:** `index.html`

Add immediately before the closing `</body>` tag:
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "GroceryStore",
  "name": "Млечко",
  "description": "Специализиран магазин за пресни млечни продукти, яйца и маслини в Пловдив.",
  "url": "https://mlechko.bg",
  "telephone": "+359878232365",
  "email": "info@mlechko.bg",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "бул. Пещерско шосе 26",
    "addressLocality": "Пловдив",
    "postalCode": "4002",
    "addressCountry": "BG"
  },
  "openingHoursSpecification": [
    {"@type": "OpeningHoursSpecification", "dayOfWeek": ["Monday","Tuesday","Wednesday","Thursday","Friday"], "opens": "08:00", "closes": "20:00"},
    {"@type": "OpeningHoursSpecification", "dayOfWeek": "Saturday", "opens": "09:00", "closes": "18:00"},
    {"@type": "OpeningHoursSpecification", "dayOfWeek": "Sunday", "opens": "10:00", "closes": "16:00"}
  ]
}
</script>
```

---

### 2.6 Create `robots.txt` and `sitemap.xml`

**Create `robots.txt`** in project root:
```
User-agent: *
Allow: /
Sitemap: https://mlechko.bg/sitemap.xml
```

**Create `sitemap.xml`** in project root:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://mlechko.bg/</loc>
    <lastmod>2026-06-30</lastmod>
    <changefreq>monthly</changefreq>
    <priority>1.0</priority>
  </url>
</urlset>
```

---

### 2.7 Add email to Visit section

**File:** `index.html`

Find the Visit section (`id="visit"`, around line 1128). There are contact blocks for Address, Hours, and Phone. Add an Email block after Phone:

```html
<div class="visit-contact-item reveal">
  <div class="visit-icon" aria-hidden="true">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><rect x="2" y="4" width="20" height="16" rx="2"/><path d="M2 7l10 7 10-7"/></svg>
  </div>
  <div class="visit-contact-text">
    <span class="visit-label">Имейл</span>
    <a href="mailto:info@mlechko.bg">info@mlechko.bg</a>
  </div>
</div>
```

Match the surrounding HTML structure (look at the existing Phone block for the exact class names).

---

### 2.8 Fix testimonial — rewrite Димитър Попов review

**File:** `index.html` JS section (testimonials data array, around line 1289–1298)

Find:
```js
{ name:'Димитър Попов', location:'Куртово', text:'Специализиран магазин с истинско отношение. Продуктите пристигат пресно всяка сутрин. Среща се разликата. Топло ви препоръчвам.' },
```

Replace `text` with something that avoids "среща се разликата" (already used in Мария Костова's review):
```js
{ name:'Димитър Попов', location:'Куртово', text:'Специализиран магазин с истинско отношение. Продуктите пристигат пресно всяка сутрин. Качеството говори само за себе си. Топло ви препоръчвам.' },
```

---

## Pass 3 — Minor (Polish)

### 3.1 Fix footer tagline opacity

**File:** `index.html` `<style>` block

Find `.footer-tagline` CSS rule. Change:
```css
color: rgba(255,255,255,0.45);
```
to:
```css
color: rgba(255,255,255,0.72);
```

---

### 3.2 Fix stats bar — stat 2 inconsistency

**File:** `index.html` around line 877 (the second `.stat-item`)

Find:
```html
<span class="stat-number">Гарантирана</span>
<span class="stat-label">Прясност Всяка Сутрин</span>
```

Replace with:
```html
<span class="stat-number">365</span>
<span class="stat-label">Дни Прясност в Годината</span>
```

*(This makes all 3 stats numeric and consistent: `100+` / `365` / `20+`)*

---

### 3.3 Add apple-touch-icon

**File:** `index.html` `<head>`

Add after the existing favicon `<link>`:
```html
<link rel="apple-touch-icon" sizes="180x180" href="images/Mlechko-Logo.png">
```

---

### 3.4 Fix `package.json` name

**File:** `package.json` line 2

Change:
```json
"name": "bokki-dairy",
```
to:
```json
"name": "mlechko-dairy",
```

---

## Verification Checklist

After all passes are complete, run through this list:

- [ ] `node serve.mjs` starts without error
- [ ] `http://localhost:3000` loads; hero video plays
- [ ] View source: only **one** `<h1>` is missing `aria-hidden` at any time
- [ ] View source: `<main id="main-content">` exists wrapping page body
- [ ] Tab through the page: skip-to-content link appears on first Tab press
- [ ] Nav CTA button ("СВЪРЖЕТЕ СЕ") is visibly darker green than before
- [ ] "2010" badge "От/Година" text is dark and legible on gold
- [ ] Stats bar shows `100+` / `365` / `20+` — all numeric
- [ ] Hero carousel has 3 dots below the text; clicking a dot switches panel
- [ ] View source `<head>`: Open Graph tags, canonical, JSON-LD present
- [ ] `robots.txt` and `sitemap.xml` exist at project root
- [ ] Visit section has email address block
- [ ] Resize to 390px width: mobile nav overlay is fully opaque dark
- [ ] Run `node screenshot.mjs http://localhost:3000 final` and inspect full-page PNG

---

## File Summary

| File | Passes that touch it |
|---|---|
| `index.html` | 1.1, 1.2, 1.3, 1.4, 1.5, 1.6, 2.1, 2.2, 2.3, 2.4, 2.5, 2.7, 2.8, 3.1, 3.2, 3.3 |
| `robots.txt` | 2.6 (create new) |
| `sitemap.xml` | 2.6 (create new) |
| `package.json` | 3.4 |
