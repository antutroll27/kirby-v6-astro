# Tailwind v4 Hybrid Migration — Design Spec

**Date:** 2026-05-19
**Status:** Approved (implicit via goal "Option B")
**Scope:** Migrate KIRBY-v6-astro to actively use Tailwind v4 utility classes throughout all 12 components

---

## Current State

Tailwind v4 is installed (`@tailwindcss/vite`, `tailwindcss` in devDependencies, `@import "tailwindcss"` + `@theme` in `global.css`) but **zero utility classes** appear in any component HTML. Every component uses custom named classes with scoped `<style>` blocks. Tailwind is wired up but dormant.

---

## Approach: Hybrid Migration

**Principle:** Tailwind handles layout, spacing, color, and typography. Scoped CSS stays only for what Tailwind can't express cleanly.

### What moves to Tailwind utilities in HTML

| Category | Examples |
|---|---|
| Flex/grid layout | `flex`, `grid`, `items-center`, `justify-between`, `grid-cols-2` |
| Gap, padding, margin | `gap-4`, `p-5`, `px-[--pad-x]`, `mt-4` |
| Background, text, border color | `bg-ink`, `text-yellow`, `border-red` (via `@theme` tokens) |
| Font family | `font-display`, `font-body`, `font-mono` |
| Font size (standard steps) | `text-xs`, `text-sm`, `text-base` |
| Font weight, tracking, case | `font-medium`, `tracking-widest`, `uppercase` |
| Position, inset, z-index | `fixed`, `absolute`, `relative`, `inset-0`, `z-10` |
| Overflow, display | `overflow-hidden`, `hidden`, `block`, `inline-flex` |
| Width/height | `w-full`, `h-full`, `w-px`, `min-h-screen` |
| Hover/focus states | `hover:text-yellow`, `hover:-translate-y-1`, `focus:outline-none` |
| Transitions | `transition-transform`, `duration-200` |
| Opacity, pointer-events | `opacity-0`, `pointer-events-none` |

### What stays in scoped `<style>` blocks

| Pattern | Reason |
|---|---|
| `clamp()` fluid font sizes | No clean Tailwind equivalent; arbitrary `text-[clamp(...)]` is unreadable |
| `@keyframes notice`, `@keyframes ticker` | Defined in `global.css`; animation name referenced in `animation:` CSS |
| `body::after` film grain SVG overlay | Complex pseudo-element with data URI — stays in global.css |
| Iframe `top: -60px` offset + `calc()` sizing | Too specific; named class is clearer |
| `repeating-linear-gradient` diagonal patterns | Complex background; stays named |
| `box-shadow: 8px 8px 0 var(--red)` offset shadows | Custom values; use `@theme` shadow token with CSS |
| `aspect-ratio: 9/16` on tiles | Use `aspect-[9/16]` utility |
| `scroll-width`-based JS measurement in shop lockup | JS, not CSS |

### `@layer components` candidates

Single-purpose classes used in multiple components move to `@layer components` in `global.css` rather than being inlined:

- `.label` — the yellow mono uppercase label with `::before` line
- `.italic-serif` — Times italic red accent text
- `.section-head` — 2-col grid heading block
- `.section-pad` — vertical section padding with `clamp()`
- `.reveal` / `.reveal.in` — scroll-in animation state

These stay as named classes (consumed in HTML as `class="label"`) but their definitions move to `@layer components` so Tailwind manages them.

---

## Token Additions to `@theme`

Current `@theme` is missing semantic tokens used throughout components as raw values. Add:

```css
@theme {
  /* already present */
  --color-yellow: #fcaf17;
  --color-red: #961e10;
  --color-red-dark: #6a1509;
  --color-cream: #f6e9c9;
  --color-ink: #120905;
  --color-ink-2: #1d1109;
  --color-fg-dim: #b9a88a;
  --color-fg-mute: #766652;

  /* add: semantic rule colors */
  --color-rule: rgba(246,233,201,0.12);
  --color-rule-strong: rgba(246,233,201,0.32);

  /* add: shadow shorthands */
  --shadow: 0 0.08em 0 #961e10;
  --shadow-sm: 0 0.06em 0 #961e10;

  /* add: layout */
  --spacing-pad-x: clamp(20px, 4vw, 64px);
  --spacing-notice-h: 32px;
}
```

---

## Component Migration Plan

All 12 components receive the same treatment:
1. Audit every CSS rule in `<style>` block
2. Move mappable rules to Tailwind classes on the element in HTML
3. Delete the CSS rule from `<style>`
4. What remains in `<style>` is only genuinely complex/unmappable CSS

### Priority order (most impactful first)

1. `Footer.astro` — simplest, mostly flex/mono/color
2. `NoticeBar.astro` — fixed positioning, flex, animation reference
3. `Ticker.astro` — animation, flex, typography
4. `Contact.astro` — grid, flex, big CTA typography
5. `Services.astro` — 2-col grid, cards
6. `Cases.astro` — grid rows, hover states
7. `VSL.astro` — 2-col grid, pricing grid, iframe facade
8. `Shop.astro` — 3-col grid, product cards, cover overlays
9. `Work.astro` — 12-col reel grid, tiles
10. `Hero.astro` — most complex; fluid type, portrait box
11. `About.astro` — portrait box, copy layout
12. `Nav.astro` — fixed nav, hamburger state, mobile drawer

---

## Success Criteria

- All 12 components migrated
- Build passes (`astro build`) with no CSS errors
- Dev server renders visually identical to pre-migration
- No raw hex values in component HTML — only token-based Tailwind classes
- Per-component `<style>` blocks reduced by ≥50% line count on average
- `global.css` `@layer components` contains all shared named classes
- No `!important` hacks introduced

---

## Out of Scope

- Any visual design changes
- Adding new sections or features
- Migrating to Tailwind's `@apply` (anti-pattern — utilities in HTML is the Tailwind way)
- Changing font loading strategy
- Adding dark/light mode toggle
