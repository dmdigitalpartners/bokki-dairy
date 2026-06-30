# Млечко Website — Professional Pre-Launch Audit Report

**Date:** 30 June 2026  
**Auditor:** Senior UX, CRO & Front-End Audit  
**Site:** http://localhost:3000 (Mlechko Dairy, Plovdiv, Bulgaria)  
**Scope:** Single-page website (`index.html`, ~73 KB, 1,366 lines)

---

## Automated Scores

| Category | Score | Verdict |
|---|---|---|
| **Lighthouse Performance** | 67 / 100 | 🔴 Poor |
| **Lighthouse Accessibility** | 95 / 100 | 🟢 Excellent |
| **Lighthouse Best Practices** | 96 / 100 | 🟢 Excellent |
| **Lighthouse SEO** | 100 / 100 | 🟢 Perfect |
| **axe-core Violations** | 3 (1 serious, 2 moderate) | 🟠 Needs fix |

### Core Web Vitals

| Metric | Value | Status |
|---|---|---|
| LCP (Largest Contentful Paint) | **29.9 s** | 🔴 Critical |
| FCP (First Contentful Paint) | 3.7 s | 🟠 Poor |
| TBT (Total Blocking Time) | 0 ms | 🟢 Perfect |
| CLS (Cumulative Layout Shift) | 0 | 🟢 Perfect |
| Speed Index | 3.7 s | 🟠 Moderate |
| TTI (Time to Interactive) | 30.6 s | 🔴 Critical |

> **Root cause of the catastrophic LCP/TTI:** The hero video file (`Hero video.mp4`) is 404ing due to a server-side URL decoding bug. Lighthouse waits ~30 s for the video timeout before registering the LCP element. Fixing this one bug will likely bring performance to 85+.

---

## 1. Visual Design Audit

**Screenshot reference:** `screenshot-1-desktop-fullpage.png`, `screenshot-3-desktop-hero.png`

### What Works Well
- Custom brand palette (`--color-sage #7C9A7E`, `--color-gold #C8A96E`, `--color-cream #FAF7F2`) is coherent, warm, and avoids generic Tailwind defaults.
- Playfair Display + Inter is an excellent editorial pairing — display serif for headlines gives a premium artisan feel.
- Tight tracking (`letter-spacing: -0.02em`) on large headings reads as modern and intentional.
- Wave SVG dividers between sections create smooth visual transitions and genuine depth.
- Category card design is strong: real product photos, gradient overlay, pill tags, clear "Разгледай →" CTAs.
- The dark About section (`#1A1A2E`) provides good contrast rhythm against the cream-background sections.
- The stats bar uses generous whitespace and vertical dividers effectively.

### Weaknesses

**Hero falls flat without video (🔴 Critical)**  
The hero background is completely grey — the video is broken. Without it, the hero looks generic: just large text over a flat grey wash. There is no visual identity. A real user visiting the production Vercel URL may or may not see the video (the MIME issue affects localhost; the broken URL encoding is the main failure).

**Eggs featured section uses wrong image (🟠 Moderate)**  
`screenshot-8-desktop-featured-eggs.png`: The "Пресни яйца, събрани днес" section displays `IMG_0708.jpg`, which is a photo of the shop exterior — a red-framed storefront with a commercial sign. This has no visual connection to eggs and destroys the content/image coherence built up by every other section. Expected: a close-up of fresh eggs on straw, a basket of eggs, or the egg display inside the shop.

**Stats bar: inconsistent data type in stat 2 (🟠 Moderate)**  
Stat 1 and Stat 3 have numbers (`100+`, `20+`). Stat 2 has the word "Гарантирана" as its primary display value, with "ПРЯСНОСТ ВСЯКА СУТРИН" as label. This asymmetry is jarring. The word is visually similar in size and weight to the numbers but carries different semantic weight. Consider: use a number (e.g. "365" with label "ДНИ ПРЯСНОСТ В ГОДИНАТА"), an icon treatment, or restructure stat 2 as a text statement with different visual styling to signal it's qualitatively different.

**About section collage image alt text mismatch (🟢 Minor)**  
`alt="Екстериор на магазин Млечко"` — this says "shop exterior," but in the mobile cropped view the images include an interior shot. Accuracy matters for screen reader users.

### Recommendations
1. Fix the video bug immediately (see Bug Hunt §16).
2. Replace `IMG_0708.jpg` in the eggs featured section with a photo of eggs.
3. Redesign stat 2 to have a numeric or icon-based primary value.

**Expected impact:** High on first impression and trust. **Priority: 🔴 Critical (items 1, 2), 🟠 Moderate (item 3)**

---

## 2. Layout & Structure Audit

**Screenshot reference:** `screenshot-1-desktop-fullpage.png`, `screenshot-14-mobile-fullpage.png`

### Section Order Assessment

| # | Section | Purpose | Assessment |
|---|---|---|---|
| 1 | Hero | First impression, CTAs | ✅ Correct position |
| 2 | Stats Bar | Trust signals | ✅ Good — immediately reinforces hero claims |
| 3 | About | Brand story | ✅ Appropriate after trust signals |
| 4 | Categories | Product browse | ✅ Natural progression |
| 5–7 | Featured Products (×3) | Deep product detail | 🟠 Three in a row creates pacing fatigue |
| 8 | Testimonials | Social proof | ✅ Good position — social proof before the ask |
| 9 | Visit / Map | Conversion | ✅ Strong close — practical action |
| 10 | Footer | Navigation / contact | ✅ Standard |

### Issues

**Three consecutive featured sections cause scroll fatigue (🟠 Moderate)**  
Dairy → Eggs → Olives presented as three identical left/right alternating blocks one after another creates a repetitive rhythm. By the third block, users have mentally mapped the pattern and skim. Consider breaking the monotony: collapse to a tabbed or accordion format, or intersperse the testimonials carousel between Dairy and Eggs/Olives.

**Excessive whitespace between featured sections on desktop (🟢 Minor)**  
There is approximately 200–250px of empty space between each featured section pair. The featured-dairy/eggs/olives sections each have large `padding: 120px 0` and the containers don't reach full bleed. This creates a floaty, disconnected feeling. Reduce inter-section gap by ~30–40%.

**No visual anchor between categories and featured products (🟢 Minor)**  
A user clicking "Разгледай →" on a category card would expect to land on a product detail. Instead, they scroll to the featured sections which have a different heading hierarchy and visual treatment. Adding a visible anchor (`id`) on each featured section and connecting the category CTAs with scroll-to would close this loop.

### Recommendations
1. Break up the three consecutive featured sections — insert testimonials between dairy and eggs.
2. Reduce padding between featured sections from 120px to 80px.
3. Wire category card CTAs directly to their respective featured sections.

**Priority: 🟠 Moderate | Effort: Low**

---

## 3. Copywriting Audit

**Language:** Bulgarian

### Headlines

| Section | Current Headline | Issue | Suggested Improvement |
|---|---|---|---|
| Hero Panel 1 | "Специализиран магазин. Истински вкус." | Describes the business ("WE") | "Истински вкус. Всяка сутрин пресен." |
| Hero Panel 2 | "Пловдивски дом на качеството." | Self-congratulatory | "Качество, което усещате от първата хапка." |
| Hero Panel 3 | "Над 30 вида продукти. Всеки ден пресни." | Better — benefit-focused ✓ | Keep as-is or add specificity |
| About | "Специализиран избор. Традиционен вкус." | Self-describing | "Изборът, на когото се доверявате всяка сутрин." |
| Categories | "Всичко пресно. Всичко избрано." | Good ✓ | — |
| Testimonials | "Думите на нашите клиенти" | Flat, expected | "Какво казват Пловдивчани" |

### CTAs

| Location | Current CTA | Issue | Suggested CTA |
|---|---|---|---|
| Hero (primary) | "НАШИТЕ ПРОДУКТИ" | Action-neutral | "РАЗГЛЕДАЙ ВСИЧКИ ПРОДУКТИ" |
| Hero (secondary) | "НАМЕРЕТЕ НИ" | Functional ✓ | Keep |
| About | "Намерете ни →" | Too passive | "Елате при нас →" |
| Category cards | "Разгледай →" | Minimal ✓ | Keep |

### Footer Tagline

"Нищо повече и нищо по-малко." — rendered at 45% opacity in tiny italic text. This is the brand's philosophical statement and it's nearly invisible. Either commit to it (larger, prominent, centered, full opacity) or replace it with a warmer positioning line: "От производителя на вашата трапеза, всяка сутрин."

### Body Copy Issues

**Repetitive testimonials (🟠 Moderate)**  
Two reviews use near-identical phrasing:
- Мария Костова: *"Истинска разлика се среща веднага."*
- Димитър Попов: *"Среща се разликата."*

This pattern makes the testimonials look machine-generated rather than authentic. The phrase "среща се разликата" is also grammatically awkward. Rewrite Димитър's testimonial with unique phrasing.

**"We" vs "You" Ratio:**  
Scanning all hero/about/feature copy, approximately 70% of sentences start with descriptions of what the shop does or is ("Специализиран магазин", "Гарантирана прясност", "Внимателно подбрани маслини"). Only 30% addresses the customer benefit directly. Recommended shift: invert this ratio. Lead with the customer outcome ("Вие получавате…", "Вашата трапеза…") and support with the how ("директно от производителя").

**Priority: 🟠 Moderate | Effort: Low**

---

## 4. Client-Oriented Messaging

The site currently speaks primarily in the **"WE"** voice. Examples of self-referential copy:

- "Специализиран магазин" (describes the business)
- "Над 30 вида продукти" (describes the inventory)
- "В бизнеса от 2010 година" (describes the history)
- "Специализиран избор. Традиционен вкус." (describes what we do)

**Recommended shift to "YOU" voice:**
- "Намерете вкус, какъвто не сте усещали от детството" → speaks to the customer's nostalgia
- "Доставено пресно преди да сте се събудили" → speaks to the customer's convenience
- "Яйца, в чиято свежест сте сигурни" → speaks to the customer's peace of mind

Every headline and CTA should answer the implicit customer question: *"What's in it for me?"*

**Priority: 🟠 Moderate | Effort: Low**

---

## 5. User Experience Audit

**Screenshot references:** All desktop section screenshots.

### Navigation

**Desktop (`screenshot-2-desktop-nav-scrolled.png`):**
- Fixed nav with backdrop blur works well — the scrolled state adds a subtle shadow.
- "СВЪРЖЕТЕ СЕ" CTA button is prominent and clearly primary. ✓
- Nav links: "Продукти | За Нас | Ревюта | Намерете Ни" — clear and scannable. ✓
- The "Ревюта" link anchors to testimonials, which is good for social proof discovery.

**Mobile (`screenshot-27-mobile-mobile-nav-open.png`):**
- Mobile nav overlay opens to full-screen dark panel with large, well-spaced links. ✓
- **Issue:** The body content is visibly bleeding through the overlay background — text from the stats/categories section is legible behind the nav links. The overlay needs `background: rgba(10, 10, 30, 0.97)` or higher opacity.

### Scroll Experience

- Staggered reveal animations on scroll feel premium and not excessive. ✓
- The 8% IntersectionObserver threshold (elements reveal when 8% visible) means elements appear very early during scroll — before the user reaches them. Consider increasing to 15–20% for more natural timing.

### Information Density

- Each section is well-scoped — no single section overwhelms.
- The featured product sections repeat the same structure (eyebrow → h2 → paragraph → 3 checkmarks → 3 dots) identically for all three products. While consistent, this feels mechanical by the third repetition.

### Trust Signals

- Stats bar provides quantified credibility. ✓
- 8 testimonials with names and Plovdiv neighborhoods adds local authenticity. ✓
- The "2010" badge on the about collage is a strong trust signal. ✓
- **Missing:** No star rating aggregate or Google/Tripadvisor link, no "Verified purchase" indicators, no prices or ordering info (is the site purely a visit-us page? If so, add a clear "Посещение" messaging).

### Friction Points

- The category card "Разгледай →" CTAs don't navigate anywhere (no anchor or external link to a product page). This creates a dead-end interaction. Either remove these CTAs or wire them to the featured sections.
- "Намерете ни →" in the About section links to `#visit`. Correct. ✓

**Priority: 🟠 Moderate (nav overlay opacity, dead-end CTAs) | Effort: Low**

---

## 6. Mobile Experience

**Screenshot references:** `screenshot-14-mobile-fullpage.png`, `screenshot-16-mobile-hero.png`, `screenshot-18-mobile-about.png`, `screenshot-23-mobile-testimonials.png`, `screenshot-27-mobile-mobile-nav-open.png`

### Hero (Mobile)

`screenshot-16-mobile-hero.png`: Center-aligned text, stacked buttons fill full width. Clean and uncluttered. The broken video manifests as an identical grey fallback on mobile. Subtitle text wraps correctly over 3 lines with comfortable line-height.

**Issue:** Two full-width stacked CTA buttons take significant vertical space — the bottom button is almost at the fold on a 390-wide device, pushing stats off screen. Consider making the secondary "НАМЕРЕТЕ НИ" button use a lighter, outlined treatment on mobile to reduce visual weight.

### Stats Bar (Mobile)

Three stats stack vertically (`screenshot-17-mobile-stats.png`). Each stat gets full-width treatment. The icons and numbers are readable. However, three stacked items take ~400px total height, creating unnecessary scroll depth before reaching the About section.

**Recommendation:** On mobile, switch to a 2+1 grid layout (first two side-by-side, third centered) or use a horizontal scroll carousel.

### Categories (Mobile)

`screenshot-18-mobile-about.png` shows categories stacking correctly to single-column cards. The full-width cards with gradient overlay and text at bottom look good. Card height appears appropriate at ~300px.

**Issue:** The "Яйца" category card shows the store sign prominently in the upper portion. This is even more noticeable on mobile than desktop because the card is wider.

### Map Section (Mobile)

`screenshot-23-mobile-testimonials.png` shows the map section: the map iframe renders correctly at mobile size, "Open in Maps" external link is accessible. Contact details stack properly above the map. ✓

### Footer (Mobile)

Footer collapses to single column correctly. Logo, tagline, social links visible. Nav links and contact details readable.

### Mobile-Specific Bugs

1. **Nav overlay opacity** (🟠): Body content bleeds through mobile menu overlay.
2. **WhatsApp FAB** (🟢): FAB is visible in all mobile screenshots at bottom-right, safe-area-inset applied. However, it overlaps the "Разгледай →" CTA on the last category card on some scroll positions.

**Mobile Score: 7/10**

---

## 7. Desktop Experience

**Screenshot references:** Desktop section screenshots.

### Navigation (Desktop)

Clean horizontal nav with centered links and right-aligned CTA. Logo scales correctly. The fixed nav's backdrop blur activates on scroll correctly.

### Content Width

Max content width appears to be ~1320px. At 1440px viewport there's appropriate padding on both sides. No content that stretches full-bleed except the hero, testimonials, and footer. ✓

### Cards and Grids

Three-column category grid at 1440px: all three cards are equal width and visually balanced. ✓  
Two-column featured sections: 55/45 split approximately. Comfortable reading line length for the text column.

### Hover States

- Category cards: image scales up + card lifts. Premium feel. ✓
- Nav links: underline slides in from left. Elegant. ✓
- CTA button: subtle scale on hover (needs verification — appeared functional in screenshot but hover states are not visible in static screenshots).
- WhatsApp FAB: scales to 1.08× on hover with green glow. ✓

### Desktop-Specific Issues

- About section: From `screenshot-5-desktop-about.png` (scrolled mid-section), the collage grid shows the main image and a small secondary image stacked. The layout reads well in dark. ✓
- Featured products: Large empty whitespace areas between sections (~150px top and bottom per section) create an airy feel that could read as poor planning on slower connections when images haven't loaded yet.

**Desktop Score: 7.5/10**

---

## 8. Responsive Design

### Breakpoints in Use

| Breakpoint | CSS | Status |
|---|---|---|
| Desktop | Default (≥1025px) | ✅ Well-designed |
| Tablet | `max-width: 1024px` | 🟠 Some layout tensions |
| Mobile | `max-width: 768px` | ✅ Generally solid |

### Tablet (`screenshot-28-tablet-fullpage.png`, `screenshot-32-tablet-about.png`)

At 1024px the layout transitions correctly:
- Three-column categories collapse to two columns (Dairy + Eggs visible, Olives below). ✓
- About collage goes to single column. ✓  
- Featured sections switch to stacked layout. ✓

**Issue:** At the 1024px breakpoint, the nav still shows the full desktop nav (not the hamburger). The "СВЪРЖЕТЕ СЕ" button text wraps at some intermediate widths (1025–1100px range). Test between 900–1024px where there may be nav overflow.

### No Mid-Range Breakpoint (🟢 Minor)

There is no breakpoint between 768px (mobile) and 1024px (tablet). On devices 768–900px (small tablets, landscape phones), the mobile layout applies. This generally works but the hero text sizing (`clamp`) may produce awkward intermediate sizes.

**Priority: 🟢 Minor | Effort: Medium**

---

## 9. Component Review

### Hero Carousel

**What works:** Smooth crossfade between panels (1.2s ease-in-out), staggered text animations (tagline slides left, h1 fades up, subtitle/buttons follow). Auto-rotation at 6s is comfortable.

**Issues:**
- No carousel navigation indicators (dots) to signal to the user that panels exist. Most users will never wait 6 seconds to see panel 2.
- No manual navigation (prev/next buttons or keyboard arrows on hero).
- All three h1 tags exist in the DOM simultaneously (inactive panels have `opacity: 0, pointer-events: none` but are not `aria-hidden`). Screen readers will read all three h1s. Recommendation: add `aria-hidden="true"` to inactive panels in JS.

### Stats Bar

- Visual design is clean. ✓
- Semantic inconsistency in stat 2 (word value vs. numeric). (See §1)

### About Section Collage

- The overlapping collage with "2010" gold badge is a nice editorial layout. ✓
- The badge text (`От / 2010 / Година`) has a contrast ratio of 1.94:1 (fails WCAG at all levels). See §10.

### Category Cards

- Best-executed component on the site. Full-height images, gradient overlay, pill tag, clear name/description/CTA. ✓
- `<article>` semantic element used correctly. ✓

### Featured Product Sections

- Alternating left/right layout works on desktop. ✓
- Three identical structures back-to-back causes pacing fatigue. (See §2)
- No CTA linking from featured section back to contact/order flow.

### Testimonials Carousel

- Smooth infinite loop carousel with touch/swipe support. ✓
- Auto-pauses on hover. ✓
- All 8 testimonials are 5 stars. Lacks authenticity — consider showing 4–5 star mix.
- Repeated phrase across reviews ("среща се разликата"). (See §3)
- No link to Google Maps / Google Reviews for external verification.

### Visit / Find Us Section

- Two-column layout (contact left, map right) is clear and useful. ✓
- Email `info@mlechko.bg` appears only in the footer, not in this section. Add it here.
- "Open in Maps" link on mobile map is discoverable. ✓

### Footer

- 4-column layout with logo, nav, contact, hours. Comprehensive. ✓
- Footer tagline too low contrast (45% opacity white on `#111120`). ✓
- Footer uses the same broken video (`Hero video.mp4`) as background — two broken video requests per page load.
- Social links (Facebook, Instagram, WhatsApp) have icon-only buttons — ensure aria-labels are present.

### WhatsApp FAB

- Well-positioned bottom-right, green brand color, tooltip on hover. ✓
- Outside any landmark region (axe violation). Wrap in a `<div role="complementary" aria-label="Контакт чрез WhatsApp">` or add to footer landmark.
- z-index 900 is appropriate and does not conflict with nav (which uses `z-index: 999`). ✓

---

## 10. Accessibility

**Automated scan:** axe-core WCAG 2.1 AA | **Lighthouse Accessibility: 95/100**

### Violations (axe-core)

#### 1. Color Contrast — `serious` (WCAG 1.4.3)

| Element | Foreground | Background | Ratio | Required | Fails |
|---|---|---|---|---|---|
| `.nav-cta` (СВЪРЖЕТЕ СЕ) | #ffffff | #7c9a7e (sage) | **3.09:1** | 4.5:1 | ✅ |
| `.btn-primary` (hero CTA) | #ffffff | #7c9a7e (sage) | **3.09:1** | 4.5:1 | ✅ |
| `.collage-badge-label` (От/Година) | #f4eee2 | #c8a96e (gold) | **1.94:1** | 4.5:1 | ✅ |

**Fix for buttons:** Darken the sage color used for button backgrounds to approximately `#4e7251` (contrast ~5.8:1 vs white). Or use white button background with dark sage text for secondary buttons.  
**Fix for badge:** Change badge text to `#2C3E50` (dark slate) on gold, or darken the gold background to `#8B6E3A`.

#### 2. No Main Landmark — `moderate` (WCAG 1.3.1, 4.1.2)

The entire page content (hero through footer) is not wrapped in a `<main>` element. Screen reader users rely on `<main>` to skip directly to primary content. Add `<main id="main-content">` wrapping all content between `<header>` and `<footer>`.

#### 3. WhatsApp FAB Outside Landmarks — `moderate`

The floating WhatsApp button is a direct child of `<body>` outside any landmark. Wrap in an `<aside>` or `role="complementary"`.

### Additional Accessibility Observations (Manual)

**Multiple h1 tags in DOM simultaneously (🔴 Critical)**  
Lines 828, 837, 846 each contain an `<h1>`. Only one panel is active (visible) at a time, but all three h1s are present in the DOM and readable by screen readers. Screen readers will announce three h1 headings in sequence. Fix: add `aria-hidden="true"` to inactive hero panels, removing it when a panel becomes active.

**No skip-to-content link (🟠 Moderate)**  
Keyboard users must tab through all 5 nav links and the CTA button before reaching the main content. Add a visually hidden "Skip to content" link as the first focusable element: `<a href="#main-content" class="skip-link">Прескочи към съдържанието</a>`.

**Focus management on mobile nav (🟢 Minor)**  
The mobile nav close button is auto-focused when opened, and hamburger re-focused on close. ✓ Good. However, the tab order within the open mobile nav should be constrained (focus trap) so tabbing cannot escape into the obscured page content below.

**Heading hierarchy:**
- h1: 3 (hero panels — issue noted above)
- h2: 4 (About, Categories, Testimonials, Visit) ✓
- h3: Used in category cards and footer — ✓

**`prefers-reduced-motion`:** CSS includes `animation-duration: 0.01ms` override. ✓

**Font sizes:** Body text is `1rem` (16px) minimum. Stat labels use `0.7rem` (11.2px) — borderline small but not body text. ✓

**Accessibility Score: 7.5/10**

---

## 11. Performance

**Lighthouse Performance: 67/100**

### Root Cause Analysis

**LCP = 29.9s: The video 404 is responsible for the entire performance failure.**

When the browser requests `Hero video.mp4`, `serve.mjs` receives `/Hero%20video.mp4` (URL-encoded), but `join(__dirname, '/Hero%20video.mp4')` returns a path to a file named literally "Hero%20video.mp4" which doesn't exist on disk. The server returns 404. The browser retries/waits for this resource extensively. Lighthouse's LCP waits for the hero area to be painted, which the video controls. With a 29.9s LCP, this site cannot pass Core Web Vitals on any platform.

**After fixing the video, the next performance bottlenecks will be:**

| Issue | Impact | Fix |
|---|---|---|
| 49.8 MB video file | 🔴 High — even if loaded, this is enormous | Compress to ≤5 MB using H.265/WebM or serve at 1080p max |
| Hero image preloaded but never rendered | 🟠 Medium — wastes ~200–500 KB of network on every load | Remove the `<link rel="preload">` for `hero-header.png` or remove that image from the build entirely |
| Images without WebP format | 🟠 Medium — JPGs are 171 KB–2.6 MB each | Convert all product images to WebP; savings typically 30–50% |
| Images missing `width`/`height` attributes | 🟠 Medium — prevents browser from reserving layout space | Add explicit dimensions to all `<img>` tags |
| Google Fonts blocking render | 🟠 Medium — `display=swap` is present ✓ but Fonts CSS is render-blocking | Already has `display=swap` ✓; consider self-hosting fonts |
| CSS unminified (13,000+ bytes) | 🟢 Low — Lighthouse flags it | Minify CSS; saves ~3–4 KB |
| Footer video: second request for same broken video | 🔴 High | After fixing, still unnecessary second load of 49.8 MB video on same page |

### Lazy Loading Status

| Image | Loading | Below fold? | Correct? |
|---|---|---|---|
| Nav logo | eager (default) | No | ✅ Correct |
| About collage imgs | lazy | Yes | ✅ |
| Category card imgs | lazy | Yes | ✅ |
| Feature section imgs | lazy | Yes | ✅ |
| Footer logo | lazy | Yes | ✅ |
| Hero image (preloaded) | eager (preload) | No — but never renders | ⚠️ Remove preload |

**Performance Score: 4/10** (Note: will likely reach 8–9/10 after video fix + compression)

---

## 12. SEO

**Lighthouse SEO: 100/100**

### Present and Correct

- ✅ `<title>`: "Млечко — Специализиран Магазин · Пловдив" — descriptive, local keyword, brand name
- ✅ `<meta name="description">`: Present, relevant, 160 chars
- ✅ `<meta name="viewport">`: Correct
- ✅ `lang="bg"`: Correct language declaration
- ✅ Heading hierarchy: Logical h1→h2→h3 structure (ignoring the multiple h1 issue)
- ✅ Image alt text: All visible images have descriptive Bulgarian alt text
- ✅ Semantic HTML: `<header>`, `<nav>`, `<section>`, `<article>`, `<footer>` used
- ✅ Google Fonts preconnect: `fonts.googleapis.com` and `fonts.gstatic.com`

### Missing (High-Value SEO Opportunities)

**Open Graph / Twitter Card Meta Tags (🟠 Moderate)**  
None present. When shared on Facebook, Messenger, WhatsApp, or Twitter/X, the link will show no image preview, no custom title, no description. Add:
```html
<meta property="og:title" content="Млечко — Специализиран магазин за млечни продукти в Пловдив">
<meta property="og:description" content="Пресни млечни продукти, яйца и маслини. Доставка всяка сутрин директно от производителите.">
<meta property="og:image" content="https://mlechko.bg/images/og-image.jpg">
<meta property="og:type" content="business.business">
<meta property="og:locale" content="bg_BG">
```

**JSON-LD Structured Data — LocalBusiness (🟠 Moderate)**  
No schema markup at all. A `LocalBusiness` schema would enable Google rich results (hours panel, address, phone in search results — high value for a local business):
```json
{
  "@context": "https://schema.org",
  "@type": "GroceryStore",
  "name": "Млечко",
  "address": {"@type": "PostalAddress", "streetAddress": "бул. Пещерско шосе 26", "addressLocality": "Пловдив", "postalCode": "4002", "addressCountry": "BG"},
  "telephone": "+359878232365",
  "openingHours": ["Mo-Fr 08:00-20:00", "Sa 09:00-18:00", "Su 10:00-16:00"],
  "url": "https://mlechko.bg"
}
```

**robots.txt and sitemap.xml (🟠 Moderate)**  
Neither file exists in the project root. While the site is currently single-page and Lighthouse's SEO score is 100 without them, they are expected by crawlers and listed in Google Search Console requirements. Create a minimal `robots.txt` and `sitemap.xml`.

**`<link rel="canonical">` (🟢 Minor)**  
No canonical URL declared. Not critical for a single-page site but good practice to prevent duplicate content issues if the site is ever served at multiple URLs (www vs non-www, HTTP vs HTTPS).

**Favicon completeness (🟢 Minor)**  
Only one favicon defined: `<link rel="icon" type="image/png" href="images/Mlechko-Logo.png">`. Missing:
- `<link rel="apple-touch-icon" href="apple-touch-icon.png">` (180×180px)
- `<link rel="icon" type="image/png" sizes="32x32" href="favicon-32x32.png">`
- `<link rel="icon" type="image/png" sizes="16x16" href="favicon-16x16.png">`

**SEO Score: 8/10** (Lighthouse 100 but missing rich-results opportunities)

---

## 13. Front-End Code Quality

**File:** Single `index.html`, ~1,366 lines, all inline styles + scripts.

### What Works Well

- CSS custom properties (variables) used consistently for all colors, fonts, spacing — very maintainable.
- No `transition-all` anywhere — CLAUDE.md rule followed. ✓
- Spring-like easing (`cubic-bezier(0.16, 1, 0.3, 1)`) used consistently across animations. ✓
- IntersectionObserver for scroll reveal (not scroll event listeners) — performant approach. ✓
- `aria-hidden="true"` on all decorative SVGs, overlays, wave dividers. ✓
- `prefers-reduced-motion` CSS override present. ✓
- Clean, commented CSS sections (NAVIGATION, HERO, STATS, ABOUT, etc.). ✓
- Mobile nav keyboard management (focus on open, refocus on close, Escape key handler). ✓

### Issues

**serve.mjs: Missing URL decode and MP4 MIME type (🔴 Critical)**  
```js
// Current (broken):
let pathname = req.url.split('?')[0];
const filePath = join(__dirname, pathname);

// Fixed:
let pathname = decodeURIComponent(req.url.split('?')[0]);
const filePath = join(__dirname, pathname);
```
Also add to MIME map: `'.mp4': 'video/mp4'`.

**Video filename has space — should be renamed (🔴 Critical)**  
`Hero video.mp4` → rename to `hero-video.mp4` and update both `<source>` tags (lines 823 and 1178). Prevents URL-encoding issues entirely regardless of server behavior.

**package.json `name` field (🟢 Minor)**  
`"name": "bokki-dairy"` should be `"name": "mlechko-dairy"`.

**Duplicate CSS patterns (🟢 Minor)**  
The `.section-header` pattern (eyebrow + h2 + subtitle) appears in 4 sections with nearly identical styles. These are repeated rather than extracted to a reusable class. Since this is a single file without a build step, this is acceptable, but if the file grows, duplication will become maintenance debt.

**Carousel JS builds DOM on every page load (🟢 Minor)**  
The testimonials carousel generates its DOM in JavaScript from a hardcoded array. This is fine for 8 items but means testimonials are invisible to JavaScript-disabled browsers and search crawlers. For a testimonials section (which has SEO value as user-generated content), consider rendering the HTML statically with the JS only adding carousel behavior.

**Code Quality Score: 7.5/10**

---

## 14. Animations

### Scroll Reveal

- Elements reveal with `opacity: 0 → 1` + `transform: translateY(30px) → 0` over 0.6s with spring easing. ✓
- Stagger delays (`reveal-delay-1` = 0.12s, `-delay-2` = 0.24s) feel natural. ✓
- `navigator.webdriver` check to auto-reveal all elements in automated testing. ✓

### Hero Carousel

- Panel crossfade at 1.2s ease-in-out. Smooth. ✓
- Text re-animates on each panel switch — confirms the content has changed. ✓
- No rotation indicator or user control for panels.

### Testimonials Carousel

- Smooth translate-based infinite loop. ✓
- Auto-pause on hover. ✓
- Touch/swipe support with 50px threshold. ✓
- Transition glitch on wrap: the infinite loop resets position instantly after the `transitionend` event — this is imperceptible if done quickly enough but worth verifying on slow devices.

### Issues

**Hero panels have no dot indicators (🟠 Moderate)**  
Without visual indicators, users have no way to know there are 3 panels or to navigate between them manually. Most users see only panel 1.

**All section reveals trigger simultaneously when scrolling fast (🟢 Minor)**  
If a user scrolls quickly through a section, all reveal elements in that section trigger at once (because all enter the viewport at the same time). This defeats the stagger animation. This is difficult to fix perfectly but adding an IntersectionObserver `rootMargin` offset can help.

**Animation consistency: Testimonials use a different motion style than reveals (🟢 Minor)**  
The testimonials carousel uses `transform: translateX()` for horizontal scroll, while all other animations use vertical (translateY). This is appropriate but worth noting — the site mixes two animation axes cleanly.

**Animation Score: 8/10**

---

## 15. Trust & Conversion

### Credibility Signals Present

| Signal | Implementation | Quality |
|---|---|---|
| Years in business ("2010") | Gold badge on About collage | ✓ Prominent |
| 20+ years experience | Stats bar | ✓ Quantified |
| 8 customer testimonials | Carousel | 🟠 All 5-star, some repetitive |
| Local Plovdiv references | Testimonial locations | ✓ Specific neighborhoods |
| Working phone number | Nav CTA + Visit section + Footer | ✓ Multiple touch points |
| Physical address | Visit section + Footer | ✓ |
| Google Maps embed | Visit section | ✓ |

### Missing Conversion Elements

| Missing Signal | Why It Matters | Recommendation |
|---|---|---|
| No Google Reviews link | Unverifiable testimonials feel generic | Add "Вижте нас в Google" button linking to GMB |
| No price signal | Customers don't know if it's affordable | "Цени като при производителя" — a copy nudge |
| No "Open now" real-time indicator | Customers may not know if they can visit today | JS-based "Отворено / Затворено" based on day/time |
| No email in Visit section | Friction for non-phone-call contacts | Add email to the visit section |
| No Instagram/social feed | Real-time product photos build trust | Consider an Instagram feed widget or at minimum a link |
| Category CTAs dead-end | "Разгледай →" leads nowhere | Wire to anchors or an order/contact flow |

### CTA Effectiveness

**Primary conversion goal of this site** appears to be driving store visits and phone calls. Both are adequately surfaced:
- Phone in nav (always visible) ✓
- Phone in Visit section ✓
- WhatsApp FAB (always visible) ✓
- Address + Map ✓

**Trust Score: 7/10** | **Conversion Score: 6.5/10**

---

## 16. Bug Hunt

### 🔴 Critical Bugs

**BUG-01: Hero video 404 — serve.mjs doesn't decode URL-encoded filenames**  
- **Location:** `serve.mjs` line 28–31, `index.html` lines 822–823
- **What happens:** Browser requests `/Hero%20video.mp4`. Server does `join(__dirname, '/Hero%20video.mp4')` → file not found → 404. LCP becomes 29.9s.
- **Console error:** `Failed to load resource: the server responded with a status of 404 (Not Found)` (captured on all 3 viewports)
- **Affected:** Both hero video AND footer video (line 1177–1178) — two broken video requests per page load.
- **Fix:**
  1. Rename file: `Hero video.mp4` → `hero-video.mp4`
  2. Update both `<source>` tags: `src="hero-video.mp4"`
  3. Add to serve.mjs MIME map: `'.mp4': 'video/mp4'`
  4. Optionally also add `decodeURIComponent()` to serve.mjs pathname handling for robustness.

**BUG-02: Three simultaneous `<h1>` tags in DOM**  
- **Location:** `index.html` lines 828, 837, 846
- **What happens:** Screen readers announce three page titles. Confuses document outline. Mildly affects SEO (Google expects one h1).
- **Fix:** In JS, when activating a panel, add `aria-hidden="true"` to the other two panels. When a panel is active, remove `aria-hidden`. The active panel's h1 is the only one visible to assistive technology.

**BUG-03: Hero image preloaded but never rendered**  
- **Location:** `index.html` line 10
- **What happens:** `<link rel="preload" as="image" href="images/hero-header.png" fetchpriority="high">` loads an image that the browser never displays (the video replaces it). This wastes network bandwidth on every page load — especially bad on mobile.
- **Fix:** Remove this preload tag. If a video fallback image is needed, use the `<video>` element's `poster` attribute instead: `<video ... poster="images/hero-header.png">`.

### 🟠 Moderate Bugs

**BUG-04: Eggs featured section displays wrong image (shop exterior, not eggs)**  
- **Location:** `index.html` line 1044, `images/IMG_0708.jpg`
- **What happens:** The "Пресни яйца, събрани днес" section shows the shop exterior sign instead of egg imagery. Content/image mismatch destroys credibility.
- **Fix:** Replace `IMG_0708.jpg` with a photo showing eggs. Note: `Enhanced images/Featured-Eggs.png` exists in the repository — verify if it shows eggs and use it if appropriate.

**BUG-05: Mobile nav overlay — body content bleeds through**  
- **Location:** `screenshot-27-mobile-mobile-nav-open.png`
- **What happens:** The categories section text is visibly legible behind the mobile nav overlay links.
- **Likely cause:** Nav overlay background alpha is insufficient. Check `.nav-mobile` background-color in CSS.
- **Fix:** Increase background opacity of `.mobile-nav` or its overlay wrapper to 0.97+.

**BUG-06: Color contrast failures on primary UI elements**  
- **Location:** All sage-colored buttons (`#7c9a7e` background with `#ffffff` text)
- **Contrast ratio:** 3.09:1 (requires 4.5:1 for AA compliance on small text)
- **Affected elements:** `.nav-cta`, `.btn-primary` (hero CTAs)
- **Fix:** Darken button background to approximately `#4e7251`. Recheck all sass-green uses.

**BUG-07: 2010 badge text — critical contrast failure**  
- **Location:** `.collage-badge-label` spans ("От" and "Година") on gold background
- **Contrast ratio:** 1.94:1 (fails at all WCAG levels)
- **Fix:** Change text color to `#2C3E50` or `#1A1A2E` (dark slate) on the gold background.

### 🟢 Minor Bugs / Notes

**BUG-08:** `package.json` name is `"bokki-dairy"` — old project name not updated.

**BUG-09:** WhatsApp FAB is outside all landmark regions (axe `region` violation).

**BUG-10:** `serve.mjs` is missing `.mp4` in its MIME map — even after renaming the file, video will be served as `application/octet-stream` without this fix.

**BUG-11:** Enhanced images directory (`Enhanced images/`) contains 7 large PNG files (1.5–3.1 MB each) that are committed to the repository but never referenced in the HTML. These inflate the repo size unnecessarily. Either integrate them or remove from git.

**BUG-12:** Footer uses the same broken video file as a background texture at 8% opacity. After the video is fixed and compressed, this doubles the data load for a purely decorative effect. Consider removing the footer video or using a static fallback image with CSS grain texture instead.

---

## 17. Priority Ranking Table

| # | Issue | Area | Priority | Impact | Effort |
|---|---|---|---|---|---|
| 1 | Hero + footer video 404 (serve.mjs URL decode + MIME type) | Performance, Bug | 🔴 Critical | LCP 29.9s → ~2s | Low |
| 2 | Three simultaneous h1 tags in DOM | Accessibility, SEO | 🔴 Critical | Screen reader confusion, weak SEO signal | Low |
| 3 | Hero image preloaded but never rendered | Performance | 🔴 Critical | Wasted bandwidth per load | Low |
| 4 | Color contrast: sage buttons (3.09:1, needs 4.5:1) | Accessibility | 🔴 Critical | WCAG AA fail on primary CTAs | Low |
| 5 | Color contrast: "2010" badge (1.94:1) | Accessibility | 🔴 Critical | WCAG fail on brand element | Low |
| 6 | No `<main>` landmark element | Accessibility | 🔴 Critical | Screen reader cannot skip to content | Low |
| 7 | Eggs section: wrong image (shop exterior instead of eggs) | Design, Trust | 🟠 Moderate | Content/image mismatch, reduces credibility | Low |
| 8 | Mobile nav overlay — body bleeds through | UX, Bug | 🟠 Moderate | Distracted mobile nav experience | Low |
| 9 | Hero carousel: no dot indicators or manual nav | UX | 🟠 Moderate | Panels 2 & 3 essentially invisible to users | Low |
| 10 | Stats bar: stat 2 uses word not number | Design | 🟠 Moderate | Visual inconsistency in key trust section | Low |
| 11 | Missing Open Graph / Twitter Card meta tags | SEO | 🟠 Moderate | No social sharing image/title previews | Low |
| 12 | Missing JSON-LD LocalBusiness schema | SEO | 🟠 Moderate | No Google rich results for local search | Medium |
| 13 | Missing robots.txt + sitemap.xml | SEO | 🟠 Moderate | Not crawl-ready for search engines | Low |
| 14 | Skip-to-content link missing | Accessibility | 🟠 Moderate | Keyboard-only users must tab through full nav | Low |
| 15 | Video file: 49.8 MB uncompressed | Performance | 🟠 Moderate | Even when fixed, enormous bandwidth cost | Medium |
| 16 | Category "Разгледай →" CTAs lead nowhere | UX, Conversion | 🟠 Moderate | Dead-end interaction, reduces conversion flow | Low |
| 17 | Testimonials: all 5-star, repetitive phrasing | Trust | 🟠 Moderate | Appears manufactured, reduces authenticity | Low |
| 18 | Copy is 70% "WE-focused" vs "YOU-focused" | Copy | 🟠 Moderate | Weaker emotional connection with visitors | Medium |
| 19 | No email address in Visit section | Conversion | 🟠 Moderate | Reduces contact options for non-callers | Low |
| 20 | WhatsApp FAB outside landmark regions | Accessibility | 🟠 Moderate | axe violation, AT users may miss it | Low |
| 21 | Images missing `width`/`height` attributes | Performance | 🟠 Moderate | Can cause CLS on slow connections | Low |
| 22 | No WebP image format | Performance | 🟠 Moderate | 30–50% larger images than necessary | High |
| 23 | Footer tagline at 45% opacity — nearly invisible | Design | 🟢 Minor | Weakens brand positioning statement | Low |
| 24 | Repeated "среща се разликата" in testimonials | Copy | 🟢 Minor | Signals copy-paste, reduces authenticity | Low |
| 25 | Hero preload uses wrong image (not the LCP element) | Performance | 🟢 Minor | Preload budget wasted | Low |
| 26 | Footer video decorative — doubles data load | Performance | 🟢 Minor | Unnecessary second video request | Low |
| 27 | package.json name "bokki-dairy" | Code | 🟢 Minor | Stale project name | Low |
| 28 | Enhanced images never used but committed | Code | 🟢 Minor | Inflates repo, creates confusion | Low |
| 29 | No apple-touch-icon / favicon variants | SEO | 🟢 Minor | Poor bookmark appearance on iOS | Low |
| 30 | No Google Reviews external link | Trust | 🟢 Minor | Unverifiable testimonials | Low |

---

## 18. Final Report

### Executive Summary

Млечко has a well-crafted, visually coherent website with a strong editorial design language, excellent semantic markup, and solid accessibility foundations. The custom brand palette, Playfair Display typography, wave dividers, and category card design create a genuinely premium feel that stands out from generic competitor sites.

However, the site has one catastrophic technical bug — the hero video 404 — that collapses the Lighthouse Performance score to 67/100 and produces a 29.9-second LCP. This single bug makes the site effectively unlaunchable in its current state; fixing it will likely raise performance to 85+/100 overnight. 

Beyond this critical fix, there are 5 additional critical accessibility issues (color contrast, missing main landmark, multiple h1 tags) that must be resolved before launch. The copy is skewed toward self-description rather than customer benefit, and two featured sections have content mismatches that undermine trust. Social sharing requires Open Graph tags that are entirely absent.

### Top 10 Highest Priority Improvements

1. **Fix video 404** — Rename `Hero video.mp4` → `hero-video.mp4`, update both `<source>` tags, add `.mp4` MIME type to `serve.mjs`, optionally add `decodeURIComponent()`. Expected impact: LCP drops from 29.9s to ~2s. ⏱ 15 minutes.

2. **Fix h1 tags** — Add `aria-hidden="true"` to inactive hero panels in JavaScript. ⏱ 10 minutes.

3. **Remove wasted preload** — Delete `<link rel="preload" as="image" href="images/hero-header.png">`. ⏱ 2 minutes.

4. **Fix color contrast on buttons** — Darken sage button color from `#7c9a7e` to `#4e7251`. ⏱ 5 minutes.

5. **Fix color contrast on 2010 badge** — Change `.collage-badge-label` text color to `#2C3E50`. ⏱ 5 minutes.

6. **Add `<main>` landmark** — Wrap content between `<header>` and `<footer>` in `<main id="main-content">`. ⏱ 10 minutes.

7. **Replace eggs section image** — Swap `IMG_0708.jpg` for a photo that actually shows eggs. ⏱ 10 minutes.

8. **Fix mobile nav overlay opacity** — Increase `.mobile-nav` background alpha to 0.97+. ⏱ 5 minutes.

9. **Add Open Graph meta tags** — 5 lines of HTML in `<head>`. ⏱ 10 minutes.

10. **Wire dead-end CTAs** — Link category "Разгледай →" buttons to `#dairy-feature`, `#eggs-feature`, `#olives-feature`. ⏱ 10 minutes.

**Total estimated time for top 10: ~80 minutes.**

### Quick Wins (High Impact, Low Effort)

- Add `<main>` element (10 min, fixes axe violation)
- Remove preload on hero-header.png (2 min, frees preload bandwidth)
- Add `aria-hidden` to inactive hero panels in JS (10 min, fixes accessibility + SEO)
- Add skip-to-content link (15 min, improves keyboard nav)
- Add OG meta tags (10 min, enables social sharing previews)
- Add `robots.txt` + `sitemap.xml` (10 min, search-crawler ready)
- Add email to Visit section (5 min, more contact options)
- Rewrite dimitar testimonial to avoid "среща се разликата" repetition (5 min)

### Medium-Term Improvements (1–2 weeks)

- Compress `Hero video.mp4` from 49.8 MB to ≤5 MB using H.265 or WebM
- Convert all product images to WebP format with `<picture>` element fallbacks
- Add `width` and `height` attributes to all product `<img>` tags
- Add JSON-LD LocalBusiness schema
- Redesign stats bar stat 2 to use a numeric format
- Add hero carousel dot indicators and manual navigation
- Shift copy from WE to YOU voice (5–6 headline rewrites)
- Add Google Reviews external link to testimonials section
- Add a "Отворено / Затворено" real-time indicator to the Visit section

### Long-Term Enhancements (1+ month)

- Consider serving Enhanced images if they are higher quality (after WebP conversion)
- Implement a simple PWA manifest for installability
- Add Google Analytics or Plausible analytics
- Explore a product catalog or ordering flow (currently site is informational only)
- Add Google My Business integration for live review import
- Consider self-hosting Google Fonts for faster FCP and privacy

---

### Scores

| Category | Score | Grade |
|---|---|---|
| **Overall Design** | **7.5 / 10** | B+ |
| **UX** | **6.5 / 10** | C+ |
| **Mobile Experience** | **7.0 / 10** | B |
| **Desktop Experience** | **7.5 / 10** | B+ |
| **Accessibility** | **7.5 / 10** | B+ |
| **Performance** | **4.0 / 10** | F (video bug) |
| **SEO** | **8.0 / 10** | B+ |
| **Conversion Optimization** | **6.5 / 10** | C+ |
| **Front-End Code Quality** | **7.5 / 10** | B+ |

> Performance score is based on current state. After the video fix + compression, expected performance score: **8.5–9.0/10**.

---

### Launch Readiness: ❌ NOT READY — Critical Fixes Required

**Verdict: Ready after critical fixes**

The site cannot launch in its current state due to:
1. 🔴 Hero video completely broken (LCP = 29.9s — unacceptable for any user or SEO)
2. 🔴 6 accessibility violations (3 confirmed by axe + 3 manual findings)
3. 🟠 Wrong image in eggs section undermines credibility

Once the 6 critical items listed in the Top 10 are resolved, the site will be in a solid state for soft launch. The remaining moderate items can be addressed in a post-launch sprint within 1–2 weeks.

**Estimated time to launch-ready: 2–4 hours of focused development.**

---

*Screenshots referenced throughout this report are located in `temporary screenshots/` at the project root. All screenshots were taken with Puppeteer v25 against the localhost:3000 development server on 30 June 2026.*
