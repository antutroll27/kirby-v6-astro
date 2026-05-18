# KIRBY Portfolio — Astro 5 + Tailwind v4 Conversion Design

**Date:** 2026-05-19  
**Status:** Implemented  
**Scope:** Convert KIRBY-v6 static HTML/CSS/JS portfolio to Astro 5 + Tailwind v4

---

## Problem Statement

KIRBY-v6 was a monolithic single-file portfolio (444-line `index.html`, 759-line `style.css`, `main.js`). It had several concrete issues blocking SEO and performance:

- No sitemap → Google had no discovery path
- `favicon.svg` referenced but file missing → crawl 404 error
- Default palette set to "poster/cream" via JS (bug) → Cumulative Layout Shift
- Tweaks/design panel shipped to real visitors in production DOM
- No mobile navigation (links hidden on ≤820px, no fallback)
- External `main.js` = extra HTTP round-trip on every load
- Dead CSS classes (`.ttl`, `.caption`) never used in HTML
- Single-file structure → hard to maintain (editing any section = scrolling 400+ lines)

---

## Approaches Considered

### Option A: Stay static, patch issues in-place
Add a sitemap.xml manually, fix the favicon.svg, strip the tweaks panel, add mobile nav. Zero new tooling.

**Pro:** No migration risk, instant.  
**Con:** Doesn't fix the maintainability problem. Adding a second page (e.g. shop, LUT detail) means duplicating the entire HTML file. Dead CSS accumulates. No automatic cache-busting.

### Option B: Astro 5 + Tailwind v4 (recommended)
Convert to Astro with one component per section. Tailwind v4 for utility classes. `@astrojs/sitemap` for automatic sitemap generation.

**Pro:** Every SEO/performance issue fixed automatically. Component architecture makes future edits surgical. Sitemap updates on every build. Content hash on CSS file for long-term browser caching. Same static HTML output — zero JS framework on the visitor's browser.  
**Con:** Migration effort (~2-3 hours). CSS bundle slightly larger (+1.2KB gzipped) due to Tailwind preflight.

### Option C: Astro + vanilla CSS (no Tailwind)
Same as B but keep the existing CSS as a global stylesheet, no Tailwind.

**Pro:** Zero CSS bundle increase.  
**Con:** Misses the component-scoped styling benefit. Still one large global CSS file.

**Decision: Option B.** The +1.2KB CSS cost is offset by −51% JS, one fewer HTTP request, and automatic sitemap/cache-busting.

---

## Architecture

```
src/
  layouts/
    Base.astro          # <head>, meta, JSON-LD, fonts, global CSS
  components/
    Nav.astro           # Fixed nav + mobile hamburger drawer
    NoticeBar.astro     # Promo marquee strip
    Hero.astro          # Full-viewport hero
    Ticker.astro        # Animated skills ticker
    Work.astro          # Instagram embed grid (8 tiles)
    VSL.astro           # YouTube facade + pricing copy
    Cases.astro         # Case study list rows
    Services.astro      # 2-column service cards
    Shop.astro          # LUT product cards
    About.astro         # Portrait + bio copy
    Contact.astro       # Big CTA + links
    Footer.astro        # Footer bar
  pages/
    index.astro         # Composes all components in order
  styles/
    global.css          # CSS custom properties (@theme), keyframes, utilities
public/
  img/                  # Reel + cover images
  favicon.ico / .svg / apple-touch-icon.png
  og-image.jpg
```

Each component owns its own scoped CSS via Astro's `<style>` blocks. Global design tokens (colors, fonts, spacing) live in `global.css` as `@theme` variables accessible to all Tailwind utilities.

---

## What Was Fixed vs Original

| Issue | Resolution |
|---|---|
| No sitemap | `@astrojs/sitemap` — auto-generates on every build |
| `favicon.svg` 404 | Created `public/favicon.svg` |
| Poster palette default bug | Removed tweaks JS entirely; dark ink is CSS default |
| Tweaks panel in production DOM | Removed entirely |
| No mobile nav | Hamburger button + ARIA-managed drawer in `Nav.astro` |
| External `main.js` round-trip | All JS inlined, minified to 1,816 bytes (was 3,730 bytes external) |
| Dead CSS (`.ttl`, `.caption`) | Not ported |
| Single-file maintenance | 12 components, each ~40-80 lines |
| No CSS cache-busting | Astro adds content hash automatically (`index.BzVL89XI.css`) |

---

## What Was Not Changed

- Visual design: identical to KIRBY-v6 (same colors, typography, layout, copy)
- All external links (Upwork, Gumroad, Instagram, LinkedIn)
- All 8 Instagram embeds (still `loading="lazy"`)
- YouTube facade pattern
- JSON-LD structured data (Person + ItemList)
- OG / Twitter meta tags
- Placeholder portrait photos (still `[PHOTO / JAMES]` — content gap, not a code problem)
- Product cover images (still gradient placeholders — awaiting real LUT preview imagery)

---

## Out of Scope

- Instagram iframe facade (replacing 8 iframes with click-to-load thumbnails) — valid future perf improvement
- Self-hosting fonts (eliminate Google Fonts round-trip) — valid future perf improvement
- Actual photo/product imagery — content, not code
- Additional pages (shop detail, about page) — future work
