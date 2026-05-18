# Tailwind v4 Hybrid Migration Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Migrate all 12 Astro components from custom scoped CSS to Tailwind v4 utility classes, keeping scoped CSS only for clamp() fluid sizes, text-shadow, complex gradients, animation references, and iframe offset tricks.

**Architecture:** Hybrid approach — Tailwind utilities in HTML for layout/color/typography/spacing, scoped `<style>` blocks kept only for genuinely inexpressible patterns. Shared named classes (.label, .italic-serif, .section-head, .section-pad, .reveal) stay in global.css under `@layer components`.

**Tech Stack:** Astro 5, Tailwind v4 (`@tailwindcss/vite`), scoped Astro `<style>` blocks

---

## Tailwind class reference used throughout this plan

| CSS value | Tailwind class |
|---|---|
| `display: flex` | `flex` |
| `display: grid` | `grid` |
| `display: none` | `hidden` |
| `display: block` | `block` |
| `display: inline-flex` | `inline-flex` |
| `position: fixed` | `fixed` |
| `position: absolute` | `absolute` |
| `position: relative` | `relative` |
| `inset: 0` | `inset-0` |
| `top:0;left:0;right:0` | `inset-x-0 top-0` |
| `overflow: hidden` | `overflow-hidden` |
| `z-index: 99` | `z-[99]` |
| `z-index: 100` | `z-[100]` |
| `z-index: 101` | `z-[101]` |
| `align-items: center` | `items-center` |
| `align-items: start` | `items-start` |
| `align-items: end` | `items-end` |
| `align-items: flex-end` | `items-end` |
| `justify-content: space-between` | `justify-between` |
| `justify-content: center` | `justify-center` |
| `flex-direction: column` | `flex-col` |
| `flex-wrap: wrap` | `flex-wrap` |
| `flex: 1 0 auto` | `grow shrink-0` |
| `flex: 0 0 auto` | `shrink-0` |
| `flex: 1 1 auto` | `grow` |
| `gap: 10px` | `gap-2.5` |
| `gap: 12px` | `gap-3` |
| `gap: 14px` | `gap-3.5` |
| `gap: 16px` | `gap-4` |
| `gap: 20px` | `gap-5` |
| `gap: 24px` | `gap-6` |
| `gap: 28px` | `gap-7` |
| `gap: 32px` | `gap-8` |
| `gap: 40px` | `gap-10` |
| `gap: 48px` | `gap-12` |
| `gap: 60px` | `gap-[60px]` |
| `width: 100%` | `w-full` |
| `height: 100%` | `h-full` |
| `height: 32px` | `h-8` |
| `height: 36px` | `h-9` |
| `height: 44px` | `h-11` |
| `height: 48px` | `h-12` |
| `height: 56px` | `h-14` |
| `height: 68px` | `h-[68px]` |
| `width: 36px` | `w-9` |
| `width: 44px` | `w-11` |
| `width: 48px` | `w-12` |
| `width: 56px` | `w-14` |
| `width: 68px` | `w-[68px]` |
| `width: max-content` | `w-max` |
| `min-width: 0` | `min-w-0` |
| `min-height: 260px` | `min-h-[260px]` |
| `min-height: 100vh` | `min-h-screen` |
| `max-width: 100%` | `max-w-full` |
| `padding: 14px` | `p-3.5` |
| `padding: 20px` | `p-5` |
| `padding: 40px 32px` | `px-8 py-10` |
| `padding-top: 20px` | `pt-5` |
| `padding-top: 24px` | `pt-6` |
| `padding-top: 30px` | `pt-[30px]` |
| `padding-left: 40px` | `pl-10` |
| `padding-left: 48px` | `pl-12` |
| `padding-bottom: 18px` | `pb-[18px]` |
| `padding-bottom: 24px` | `pb-6` |
| `margin: 0` | `m-0` |
| `margin-top: 8px` | `mt-2` |
| `margin-top: 12px` | `mt-3` |
| `margin-top: 16px` | `mt-4` |
| `margin-top: 18px` | `mt-[18px]` |
| `margin-top: 20px` | `mt-5` |
| `margin-top: 24px` | `mt-6` |
| `margin-top: 40px` | `mt-10` |
| `margin-bottom: 4px` | `mb-1` |
| `margin-bottom: 8px` | `mb-2` |
| `margin-bottom: 14px` | `mb-3.5` |
| `margin-bottom: 36px` | `mb-9` |
| `background-color: #120905` | `bg-ink` |
| `background-color: #1d1109` | `bg-ink-2` |
| `background-color: #fcaf17` | `bg-yellow` |
| `background-color: #000` | `bg-black` |
| `color: #fcaf17` | `text-yellow` |
| `color: #961e10` | `text-red` |
| `color: #6a1509` | `text-red-dark` |
| `color: #f6e9c9` | `text-cream` |
| `color: #b9a88a` | `text-fg-dim` |
| `color: #766652` | `text-fg-mute` |
| `color: #120905` | `text-ink` |
| `color: #fff` | `text-white` |
| `border-color: rgba(246,233,201,0.12)` | `border-rule` |
| `border-color: rgba(246,233,201,0.32)` | `border-rule-strong` |
| `border: 1px solid …` | `border border-[color]` |
| `border-top: 1px solid …` | `border-t border-[color]` |
| `border-bottom: 1px solid …` | `border-b border-[color]` |
| `border-top: 2px solid …` | `border-t-2 border-[color]` |
| `border-bottom: 2px solid …` | `border-b-2 border-[color]` |
| `border-top+bottom: 2px` | `border-y-2 border-[color]` |
| `border: 2px solid …` | `border-2 border-[color]` |
| `border: 1.5px solid …` | `border-[1.5px] border-[color]` |
| `font-family: Alfa Slab` | `font-display` |
| `font-family: Inter` | `font-body` |
| `font-family: JetBrains Mono` | `font-mono` |
| `font-family: Times New Roman` | `font-serif` (add to @theme) |
| `font-weight: 400` | `font-normal` |
| `font-weight: 500` | `font-medium` |
| `font-weight: 700` | `font-bold` |
| `font-style: italic` | `italic` |
| `text-transform: uppercase` | `uppercase` |
| `text-transform: none` | `normal-case` |
| `letter-spacing: 0` | `tracking-normal` |
| `letter-spacing: 0.02em` | `tracking-[0.02em]` |
| `letter-spacing: 0.1em` | `tracking-[0.1em]` |
| `letter-spacing: 0.12em` | `tracking-[0.12em]` |
| `letter-spacing: 0.14em` | `tracking-[0.14em]` |
| `letter-spacing: 0.16em` | `tracking-[0.16em]` |
| `letter-spacing: 0.18em` | `tracking-[0.18em]` |
| `letter-spacing: 0.2em` | `tracking-[0.2em]` |
| `letter-spacing: 0.22em` | `tracking-[0.22em]` |
| `letter-spacing: -0.01em` | `tracking-[-0.01em]` |
| `letter-spacing: -0.02em` | `tracking-[-0.02em]` |
| `letter-spacing: -0.03em` | `tracking-[-0.03em]` |
| `line-height: 1` | `leading-none` |
| `line-height: 0.88` | `leading-[0.88]` |
| `line-height: 0.9` | `leading-[0.9]` |
| `line-height: 0.92` | `leading-[0.92]` |
| `line-height: 0.95` | `leading-[0.95]` |
| `line-height: 1.4` | `leading-[1.4]` |
| `line-height: 1.45` | `leading-[1.45]` |
| `line-height: 1.5` | `leading-[1.5]` |
| `line-height: 1.55` | `leading-[1.55]` |
| `font-size: 9px` | `text-[9px]` |
| `font-size: 10px` | `text-[10px]` |
| `font-size: 11px` | `text-[11px]` |
| `font-size: 12px` | `text-xs` (0.75rem) |
| `font-size: 13px` | `text-[13px]` |
| `font-size: 14px` | `text-sm` |
| `font-size: 15px` | `text-[15px]` |
| `font-size: 18px` | `text-lg` (1.125rem≈18px) |
| `font-size: 19px` | `text-[19px]` |
| `font-size: 22px` | `text-[22px]` |
| `font-size: 24px` | `text-[24px]` |
| `font-size: 26px` | `text-[26px]` |
| `object-fit: cover` | `object-cover` |
| `border-radius: 999px` | `rounded-full` |
| `border-radius: 12px` | `rounded-xl` |
| `white-space: nowrap` | `whitespace-nowrap` |
| `cursor: pointer` | `cursor-pointer` |
| `pointer-events: none` | `pointer-events-none` |
| `place-items: center` | `place-items-center` |
| `backdrop-filter: blur(8px)` | `backdrop-blur-sm` |
| `backdrop-filter: blur(12px)` | `backdrop-blur-md` |
| `transition: transform 0.15s` | `transition-transform duration-150` |
| `transition: transform 0.2s` | `transition-transform duration-200` |
| `transition: transform 0.3s` | `transition-transform duration-300` |
| `transition: transform 0.35s ease` | `transition-transform duration-[350ms]` |
| `aspect-ratio: 16/9` | `aspect-video` |
| `aspect-ratio: 4/5` | `aspect-[4/5]` |
| `aspect-ratio: 9/16` | `aspect-[9/16]` |
| `grid-template-columns: repeat(2,1fr)` | `grid-cols-2` |
| `grid-template-columns: repeat(3,minmax(0,1fr))` | `grid-cols-3` |
| `grid-template-columns: repeat(4,1fr)` | `grid-cols-4` |
| `grid-template-columns: repeat(12,1fr)` | `grid-cols-12` (add to @theme) |
| `grid-column: span 3` | `col-span-3` |
| `grid-column: span 6` | `col-span-6` |
| `grid-column: span 12` | `col-span-12` |
| `padding-top: 5vh` | `pt-[5vh]` |
| `text-wrap: pretty` | `text-pretty` (Tailwind v4) |
| `align-self: flex-start` | `self-start` |

---

## Task 1: global.css — Add missing tokens + @layer components

**Files:**
- Modify: `src/styles/global.css`

- [ ] **Step 1: Add missing @theme tokens and move shared classes into @layer components**

Replace the entire contents of `src/styles/global.css` with:

```css
@import "tailwindcss";

@theme {
  --color-yellow:       #fcaf17;
  --color-red:          #961e10;
  --color-red-dark:     #6a1509;
  --color-cream:        #f6e9c9;
  --color-ink:          #120905;
  --color-ink-2:        #1d1109;
  --color-fg-dim:       #b9a88a;
  --color-fg-mute:      #766652;
  --color-rule:         rgba(246,233,201,0.12);
  --color-rule-strong:  rgba(246,233,201,0.32);

  --font-display: "Alfa Slab One", "Times New Roman", serif;
  --font-serif:   "Times New Roman", Times, serif;
  --font-body:    "Inter", system-ui, sans-serif;
  --font-mono:    "JetBrains Mono", ui-monospace, Menlo, monospace;
}

/* ── Keyframes ─────────────────────────────────────────────── */
@keyframes notice {
  from { transform: translateX(0); }
  to   { transform: translateX(-50%); }
}
@keyframes ticker {
  from { transform: translateX(0); }
  to   { transform: translateX(-50%); }
}

/* ── Global resets ─────────────────────────────────────────── */
*, *::before, *::after { box-sizing: border-box; }

html, body {
  margin: 0;
  padding: 0;
  background: #120905;
  color: #f6e9c9;
}

body {
  font-family: "Inter", system-ui, sans-serif;
  font-size: 16px;
  line-height: 1.55;
  -webkit-font-smoothing: antialiased;
  text-rendering: optimizeLegibility;
  overflow-x: hidden;
}

a { color: inherit; text-decoration: none; }
img { max-width: 100%; display: block; }
::selection { background: #fcaf17; color: #120905; }

/* ── Film grain overlay ────────────────────────────────────── */
body::after {
  content: "";
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 200;
  background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='280' height='280'><filter id='n'><feTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='2' stitchTiles='stitch'/><feColorMatrix values='0 0 0 0 0.98  0 0 0 0 0.7  0 0 0 0 0.1  0 0 0 0 0.08 0'/></filter><rect width='100%' height='100%' filter='url(%23n)'/></svg>");
  mix-blend-mode: overlay;
  opacity: 0.6;
}

/* ── Shared component classes ──────────────────────────────── */
@layer components {
  .section-pad { padding: clamp(56px, 8vh, 110px) clamp(20px, 4vw, 64px); }

  .label {
    font-family: var(--font-mono);
    font-size: 12px;
    letter-spacing: 0.18em;
    text-transform: uppercase;
    color: var(--color-yellow);
    font-weight: 500;
    display: flex;
    align-items: center;
    gap: 10px;
  }
  .label::before {
    content: "";
    width: 28px;
    height: 2px;
    background: var(--color-yellow);
  }

  .section-head {
    display: grid;
    grid-template-columns: 1.1fr 1.4fr;
    gap: 40px;
    align-items: end;
    margin-bottom: 36px;
    padding-bottom: 18px;
    border-bottom: 1px solid var(--color-rule);
  }
  .section-head h2 {
    font-family: var(--font-display);
    font-weight: 400;
    font-size: clamp(44px, 7vw, 96px);
    line-height: 0.92;
    letter-spacing: 0.005em;
    margin: 16px 0 0;
    text-transform: uppercase;
    color: var(--color-yellow);
    text-shadow: 0 0.08em 0 var(--color-red);
  }
  .section-head p {
    font-family: var(--font-serif);
    font-style: italic;
    font-size: clamp(18px, 1.5vw, 24px);
    color: var(--color-fg-dim);
    max-width: 54ch;
    text-wrap: pretty;
    margin: 0 0 4px;
    line-height: 1.4;
  }
  @media (max-width: 860px) { .section-head { grid-template-columns: 1fr; gap: 20px; } }

  .italic-serif {
    font-family: var(--font-serif);
    font-style: italic;
    font-weight: 400;
    letter-spacing: -0.015em;
    color: var(--color-red);
    text-shadow: none;
    text-transform: none;
  }

  .reveal {
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.7s ease, transform 0.7s ease;
  }
  .reveal.in {
    opacity: 1;
    transform: none;
  }

  .sr-only {
    position: absolute;
    width: 1px; height: 1px;
    padding: 0; margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border: 0;
  }

  .skip-link {
    position: absolute;
    top: -100%;
    left: 0;
    background: var(--color-yellow);
    color: var(--color-ink);
    padding: 10px 16px;
    font-family: var(--font-mono);
    font-size: 12px;
    letter-spacing: 0.14em;
    text-transform: uppercase;
    z-index: 9999;
    font-weight: 500;
    border: 2px solid var(--color-red);
  }
  .skip-link:focus { top: 0; }
}
```

- [ ] **Step 2: Verify dev server still loads**

```bash
# dev server should already be running at http://localhost:4321
# open the browser and confirm the page renders visually identical
```

Expected: page looks the same, no CSS errors in console.

- [ ] **Step 3: Commit**

```bash
cd /Users/antarikshakumar/Downloads/JK/KIRBY-v6-astro
git add src/styles/global.css
git commit -m "feat: add rule/rule-strong/font-serif tokens, wrap shared classes in @layer components"
```

---

## Task 2: Footer.astro

**Files:**
- Modify: `src/components/Footer.astro`

- [ ] **Step 1: Replace with utility-first version**

```astro
---
---
<footer class="flex justify-between flex-wrap gap-5 font-mono text-[11px] tracking-[0.1em] uppercase text-fg-mute border-t border-rule">
  <span class="mark font-display text-sm text-yellow tracking-[0.02em] uppercase">KIRBY</span>
  <span>© 2026 · jameskirbynostalgia.com</span>
  <span>Available on Upwork · Chiang Mai → Worldwide</span>
</footer>

<style>
footer { padding: 24px clamp(20px, 4vw, 64px); }
.mark  { text-shadow: 0 0.06em 0 var(--color-red); }
</style>
```

- [ ] **Step 2: Verify footer renders correctly** — check font, color, layout at bottom of page.

- [ ] **Step 3: Commit**

```bash
git add src/components/Footer.astro
git commit -m "feat: migrate Footer to Tailwind utilities"
```

---

## Task 3: NoticeBar.astro

**Files:**
- Modify: `src/components/NoticeBar.astro`

- [ ] **Step 1: Replace with utility-first version**

```astro
---
---
<div class="fixed inset-x-0 top-0 z-[101] h-8 bg-yellow text-red font-mono text-[11px] tracking-[0.22em] uppercase font-medium border-b-2 border-red overflow-hidden" aria-label="Shop promo">
  <div class="n-marquee flex gap-10 whitespace-nowrap w-max pl-10 h-full items-center">
    <span>KIRBY LUTs on Gumroad</span><span>◉</span>
    <span class="font-serif italic font-normal normal-case tracking-normal text-[13px]">Film emulation that ships</span><span>◉</span>
    <span>KIRBY LUTs on Gumroad</span><span>◉</span>
    <span class="font-serif italic font-normal normal-case tracking-normal text-[13px]">Film emulation that ships</span><span>◉</span>
    <span>KIRBY LUTs on Gumroad</span><span>◉</span>
    <span class="font-serif italic font-normal normal-case tracking-normal text-[13px]">Film emulation that ships</span><span>◉</span>
    <span>KIRBY LUTs on Gumroad</span><span>◉</span>
    <span class="font-serif italic font-normal normal-case tracking-normal text-[13px]">Film emulation that ships</span><span>◉</span>
  </div>
</div>

<style>
.n-marquee { animation: notice 60s linear infinite; }
</style>
```

- [ ] **Step 2: Verify yellow promo bar still scrolls at top of page.**

- [ ] **Step 3: Commit**

```bash
git add src/components/NoticeBar.astro
git commit -m "feat: migrate NoticeBar to Tailwind utilities"
```

---

## Task 4: Ticker.astro

**Files:**
- Modify: `src/components/Ticker.astro`

- [ ] **Step 1: Replace with utility-first version**

```astro
---
---
<div class="relative overflow-hidden border-y-2 border-red bg-yellow text-red" aria-hidden="true">
  <div class="ticker-track flex gap-12 py-3.5 pl-12 whitespace-nowrap w-max font-display text-[22px] uppercase tracking-[0.02em]">
    <span>Colour Grading</span><span class="text-red-dark">◉</span>
    <span class="font-serif italic font-bold normal-case">Short-form that moves</span><span class="text-red-dark">◉</span>
    <span>DaVinci Resolve</span><span class="text-red-dark">◉</span>
    <span class="font-serif italic font-bold normal-case">Kodak Vision3</span><span class="text-red-dark">◉</span>
    <span>KIRBY LUTs</span><span class="text-red-dark">◉</span>
    <span class="font-serif italic font-bold normal-case">Halation · Bloom · Grain</span><span class="text-red-dark">◉</span>
    <span>Chiang Mai · Worldwide</span><span class="text-red-dark">◉</span>
    <span>Colour Grading</span><span class="text-red-dark">◉</span>
    <span class="font-serif italic font-bold normal-case">Short-form that moves</span><span class="text-red-dark">◉</span>
    <span>DaVinci Resolve</span><span class="text-red-dark">◉</span>
    <span class="font-serif italic font-bold normal-case">Kodak Vision3</span><span class="text-red-dark">◉</span>
    <span>KIRBY LUTs</span><span class="text-red-dark">◉</span>
    <span class="font-serif italic font-bold normal-case">Halation · Bloom · Grain</span><span class="text-red-dark">◉</span>
    <span>Chiang Mai · Worldwide</span><span class="text-red-dark">◉</span>
  </div>
</div>

<style>
.ticker-track { animation: ticker 48s linear infinite; }
</style>
```

- [ ] **Step 2: Verify ticker scrolls correctly below hero.**

- [ ] **Step 3: Commit**

```bash
git add src/components/Ticker.astro
git commit -m "feat: migrate Ticker to Tailwind utilities"
```

---

## Task 5: Contact.astro

**Files:**
- Modify: `src/components/Contact.astro`

- [ ] **Step 1: Replace with utility-first version**

```astro
---
---
<section class="contact border-t border-rule" id="contact">
  <div class="grid gap-10">
    <div class="label reveal">§ 07 / Book it</div>
    <h2 class="big-cta font-display leading-none tracking-[0.005em] uppercase m-0 text-yellow reveal">
      <a class="block leading-none transition-transform duration-200"
         href="https://www.upwork.com/freelancers/~01507e9897388a0aed?mp_source=share"
         target="_blank" rel="noopener noreferrer">
        Make it <span class="italic-serif">look</span><br />like film →
      </a>
    </h2>
    <div class="contact-meta reveal">
      <div><b>Primary</b><a href="https://www.upwork.com/freelancers/~01507e9897388a0aed?mp_source=share" target="_blank" rel="noopener noreferrer">upwork.com/~jameskirby</a></div>
      <div><b>LinkedIn</b><a href="https://www.linkedin.com/in/jameskirbymain/" target="_blank" rel="noopener noreferrer">linkedin.com/in/jameskirbymain</a></div>
      <div><b>Instagram</b><a href="https://instagram.com/jameskirbynostalgia" target="_blank" rel="noopener noreferrer">@jameskirbynostalgia</a></div>
      <div><b>Shop</b><a href="https://jameskirbynostalgia.gumroad.com/" target="_blank" rel="noopener noreferrer">jameskirbynostalgia.gumroad.com</a></div>
    </div>
  </div>
</section>

<style>
.contact { padding: clamp(48px, 7vh, 90px) clamp(20px, 4vw, 64px) clamp(40px, 6vh, 80px); }

.big-cta { font-size: clamp(56px, 12vw, 200px); text-shadow: 0 0.08em 0 var(--color-red); }
.big-cta a:hover { transform: translate(-4px, -4px); }

.contact-meta {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 24px;
  border-top: 1px solid var(--color-rule);
  padding-top: 30px;
  font-family: var(--font-mono);
  font-size: 12px;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--color-cream);
}
.contact-meta b { display: block; color: var(--color-red); font-weight: 500; margin-bottom: 8px; font-size: 10px; }
.contact-meta a:hover { color: var(--color-yellow); }
@media (max-width: 720px) { .contact-meta { grid-template-columns: 1fr; } }
</style>
```

- [ ] **Step 2: Verify contact section and big CTA render correctly.**

- [ ] **Step 3: Commit**

```bash
git add src/components/Contact.astro
git commit -m "feat: migrate Contact to Tailwind utilities"
```

---

## Task 6: Services.astro

**Files:**
- Modify: `src/components/Services.astro`

- [ ] **Step 1: Replace with utility-first version**

```astro
---
---
<section class="section-pad bg-ink-2 border-y border-rule" id="services">
  <div class="section-head reveal">
    <div>
      <div class="label">§ 04 / Services</div>
      <h2>What I <span class="italic-serif">do</span>.</h2>
    </div>
    <p>Two lanes, kept narrow. Project-based or monthly retainer. Delivery in DaVinci Resolve on custom KIRBY PowerGrades.</p>
  </div>

  <div class="services grid grid-cols-2 gap-px bg-rule border border-rule reveal">
    <div class="service bg-ink-2 px-8 py-10 min-h-[260px] flex flex-col justify-between transition-colors duration-300 hover:bg-ink">
      <span class="num font-mono text-xs tracking-[0.16em] text-yellow">/ 01 — COLOUR</span>
      <div>
        <h3 class="service-h3 font-display font-normal uppercase text-yellow mt-10 mb-3.5 leading-[0.95]">Colour grade</h3>
        <p class="text-[15px] leading-[1.55] text-fg-dim m-0 text-pretty max-w-[44ch]">Commercial, short-form, music videos, course content. Log → final look. Node trees shared, revisions until it feels right. Halation, grain and bloom baked in when the frame wants it.</p>
      </div>
    </div>
    <div class="service bg-ink-2 px-8 py-10 min-h-[260px] flex flex-col justify-between transition-colors duration-300 hover:bg-ink">
      <span class="num font-mono text-xs tracking-[0.16em] text-yellow">/ 02 — SHORT-FORM</span>
      <div>
        <h3 class="service-h3 font-display font-normal uppercase text-yellow mt-10 mb-3.5 leading-[0.95]">Short-form content</h3>
        <p class="text-[15px] leading-[1.55] text-fg-dim m-0 text-pretty max-w-[44ch]">End-to-end reels, TikToks and Shorts for founders, creators and brands. Hook, edit, grade, delivery. I run the content engine at CMD Careers and know how to make shorts land.</p>
      </div>
    </div>
  </div>
</section>

<style>
.service-h3 { font-size: clamp(28px, 3.4vw, 44px); text-shadow: 0 0.08em 0 var(--color-red); }
@media (max-width: 720px) { .services { grid-template-columns: 1fr; } }
</style>
```

- [ ] **Step 2: Verify services grid and hover states work.**

- [ ] **Step 3: Commit**

```bash
git add src/components/Services.astro
git commit -m "feat: migrate Services to Tailwind utilities"
```

---

## Task 7: Cases.astro

**Files:**
- Modify: `src/components/Cases.astro`

- [ ] **Step 1: Replace with utility-first version**

```astro
---
---
<section class="section-pad" id="cases">
  <div class="section-head reveal">
    <div>
      <div class="label">§ 03 / Case studies</div>
      <h2>Results <span class="italic-serif">on tape</span>.</h2>
    </div>
    <p>Two current clients. Numbers, not adjectives.</p>
  </div>

  <div class="flex flex-col">
    <a class="case reveal" href="https://cassieschoonover.com/orderpending1u1m-b" target="_blank" rel="noopener noreferrer">
      <span class="font-mono text-xs text-fg-mute tracking-[0.14em] font-medium">01</span>
      <span class="client-mark w-14 h-14 border-[1.5px] border-rule-strong bg-ink-2 flex items-center justify-center overflow-hidden text-yellow font-display text-[18px] leading-none px-1">C</span>
      <span class="client-name font-serif italic font-normal tracking-[-0.01em] text-cream leading-none normal-case">Cassie Schoonover</span>
      <span class="font-mono text-[11px] tracking-[0.12em] uppercase text-fg-dim font-medium">Course grade · VSL · reels</span>
      <span class="font-mono text-[13px] tracking-[0.12em] uppercase text-fg-dim font-medium stat-text"><b class="text-red font-light text-[24px] font-display tracking-[0.02em] mr-[0.5em]"><span class="mr-[0.2em]">$</span>8K+</b> course revenue · week 1</span>
      <span class="arrow w-11 h-11 border border-red rounded-full grid place-items-center text-red transition-all duration-200">
        <svg width="14" height="14" viewBox="0 0 14 14" fill="none" stroke="currentColor" stroke-width="1.4">
          <path d="M3 11 L11 3 M5 3 H11 V9"/>
        </svg>
      </span>
    </a>
    <a class="case reveal" href="https://www.instagram.com/reel/DTK_xIYCNiS/" target="_blank" rel="noopener noreferrer">
      <span class="font-mono text-xs text-fg-mute tracking-[0.14em] font-medium">02</span>
      <span class="client-mark tight w-14 h-14 border-[1.5px] border-rule-strong bg-ink-2 flex items-center justify-center overflow-hidden text-yellow font-display text-[13px] tracking-[0.02em] leading-none px-1">CMD</span>
      <span class="client-name font-serif italic font-normal tracking-[-0.01em] text-cream leading-none normal-case">CMD Careers</span>
      <span class="font-mono text-[11px] tracking-[0.12em] uppercase text-fg-dim font-medium">Grade · edit · content lead</span>
      <span class="font-mono text-[13px] tracking-[0.12em] uppercase text-fg-dim font-medium stat-text"><b class="text-red font-light text-[24px] font-display tracking-[0.02em] mr-[0.5em]">Top performers</b> on the channel</span>
      <span class="arrow w-11 h-11 border border-red rounded-full grid place-items-center text-red transition-all duration-200">
        <svg width="14" height="14" viewBox="0 0 14 14" fill="none" stroke="currentColor" stroke-width="1.4">
          <path d="M3 11 L11 3 M5 3 H11 V9"/>
        </svg>
      </span>
    </a>
  </div>
</section>

<style>
.case {
  display: grid;
  grid-template-columns: 44px 56px 1fr 1.1fr 1fr auto;
  gap: 24px;
  align-items: center;
  padding: 24px 0;
  border-top: 1px solid var(--color-rule);
  cursor: pointer;
  transition: padding 0.3s, background 0.3s;
}
.case:last-child { border-bottom: 1px solid var(--color-rule); }
.case:hover { padding-left: 16px; padding-right: 16px; background: rgba(252,175,23,0.06); }
.case:hover .arrow { background: var(--color-yellow); color: var(--color-ink); transform: rotate(-45deg); }

.client-name { font-size: clamp(22px, 2.2vw, 32px); }

@media (max-width: 860px) {
  .case { grid-template-columns: 40px 48px 1fr auto; gap: 14px; }
  .stat-text { display: none; }
  .case span:nth-child(4) { display: none; }
  .client-mark { width: 48px !important; height: 48px !important; }
}
</style>
```

- [ ] **Step 2: Verify case rows render with correct grid, hover expands left/right, arrow rotates.**

- [ ] **Step 3: Commit**

```bash
git add src/components/Cases.astro
git commit -m "feat: migrate Cases to Tailwind utilities"
```

---

## Task 8: VSL.astro

**Files:**
- Modify: `src/components/VSL.astro`

- [ ] **Step 1: Replace with utility-first version**

```astro
---
---
<section class="section-pad bg-ink-2 border-y border-rule" id="vsl">
  <div class="vsl grid gap-10 items-start reveal">
    <div class="vsl-frame relative aspect-video border-2 border-red overflow-hidden bg-black">
      <div class="yt-facade absolute inset-0 cursor-pointer" data-id="LthQSsJXFpA" data-title="James Kirby — Video Editor &amp; Colourist showreel" role="button" tabindex="0" aria-label="Play showreel video">
        <img class="w-full h-full object-cover block" src="https://img.youtube.com/vi/LthQSsJXFpA/maxresdefault.jpg" alt="James Kirby showreel" width="1280" height="720" />
        <span class="yt-play absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[68px] h-12 rounded-xl bg-black/70 flex items-center justify-center transition-colors duration-200" aria-hidden="true"></span>
      </div>
    </div>
    <div class="vsl-copy">
      <div class="label">§ 02 / Editing &amp; colour for creators</div>
      <h2 class="vsl-h2 font-display font-normal leading-none tracking-[-0.02em] mt-3 mb-5 uppercase text-yellow">I edit &amp; <span class="italic-serif">colour grade</span> your videos.</h2>
      <div class="pitch font-serif italic text-fg-dim text-[19px] max-w-[42ch] text-pretty leading-[1.45]">
        <p class="mb-3.5">Cinematic or corporate. Hollywood-grade plugins, custom LUTs &amp; PowerGrades built from real film stocks.</p>
        <p class="mb-3.5">4–5 day turnaround. DM me or CMD Careers.</p>
      </div>
      <div class="grid grid-cols-2 gap-3 border-t border-rule pt-5 mt-6">
        <div class="pr border border-rule-strong p-3.5 font-mono text-[11px] tracking-[0.1em] uppercase text-fg-dim">
          <b class="block text-yellow font-medium font-display text-[26px] tracking-[0.01em] mb-1">$25</b>per reel
        </div>
        <div class="pr border border-rule-strong p-3.5 font-mono text-[11px] tracking-[0.1em] uppercase text-fg-dim">
          <b class="block text-yellow font-medium font-display text-[26px] tracking-[0.01em] mb-1">$100</b>per month, consistent
        </div>
      </div>
      <div class="mt-5 font-mono text-[10px] tracking-[0.14em] uppercase text-fg-mute">
        Price rises to $35 / $140 after the first 5 clients ·
        <a class="text-yellow border-b border-yellow" href="https://www.linkedin.com/in/jameskirbymain/" target="_blank" rel="noopener noreferrer">LinkedIn</a>
      </div>
    </div>
  </div>
</section>

<script>
  document.querySelectorAll('.yt-facade').forEach(el => {
    function activate() {
      const id = (el as HTMLElement).dataset.id!;
      const title = (el as HTMLElement).dataset.title || 'Video';
      const iframe = document.createElement('iframe');
      iframe.src = `https://www.youtube.com/embed/${id}?autoplay=1&rel=0&modestbranding=1`;
      iframe.title = title;
      iframe.allow = 'accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture';
      iframe.allowFullscreen = true;
      el.replaceWith(iframe);
    }
    el.addEventListener('click', activate);
    el.addEventListener('keydown', (e) => {
      const ke = e as KeyboardEvent;
      if (ke.key === 'Enter' || ke.key === ' ') activate();
    });
  });
</script>

<style>
.vsl { grid-template-columns: 1.6fr 1fr; }
@media (max-width: 900px) { .vsl { grid-template-columns: 1fr; } }

.vsl-frame { box-shadow: 10px 10px 0 var(--color-red); }
.vsl-frame iframe { position: absolute; inset: 0; width: 100%; height: 100%; border: 0; }
.yt-play::after { content: '▶'; font-size: 22px; color: #fff; padding-left: 4px; }
.yt-facade:hover .yt-play,
.yt-facade:focus .yt-play { background: var(--color-red); outline: none; }

.vsl-h2 { font-size: clamp(40px, 5.5vw, 72px); text-shadow: 0 0.08em 0 var(--color-red); }
</style>
```

- [ ] **Step 2: Verify VSL section renders — video thumbnail, play button, pricing cards, pricing text.**

- [ ] **Step 3: Commit**

```bash
git add src/components/VSL.astro
git commit -m "feat: migrate VSL to Tailwind utilities"
```

---

## Task 9: Shop.astro

**Files:**
- Modify: `src/components/Shop.astro`

- [ ] **Step 1: Replace with utility-first version**

```astro
---
const products = [
  { lot: "LOT 01", stock: "V3·250D", title: "Nostalgia Pack",  desc: "12 LUTs. Warm sun-faded Kodak look across multiple stocks.",         price: "$49", gradient: "linear-gradient(135deg, #3b1f0d 0%, #6b3016 40%, #fcaf17 100%)" },
  { lot: "LOT 02", stock: "V3·500T", title: "Cold Halation",   desc: "Tungsten PowerGrade. Halation + bloom baked in for low light.",      price: "$59", gradient: "linear-gradient(135deg, #0f1b22 0%, #1e3540 45%, #b8c4c2 100%)" },
  { lot: "LOT 03", stock: "V3·50D",  title: "Golden 50D",      desc: "Bright daylight stock. Amber highlights, honest skin tones.",        price: "$39", gradient: "linear-gradient(135deg, #2a1a08 0%, #961e10 55%, #fcaf17 100%)" },
];
---
<section class="section-pad" id="shop">
  <div class="grid gap-8">
    <div class="shop-head grid gap-12 items-end pb-6 border-b border-rule reveal">
      <div class="flex flex-col gap-3 min-w-0">
        <div class="label">§ 05 / Shop</div>
        <h3 class="shop-lockup font-display uppercase text-yellow m-0 block w-max max-w-full">
          <span class="line block leading-[0.88] w-max max-w-full">KIRBY</span>
          <span class="line-small block leading-none whitespace-nowrap" style="font-size: 0.53em; letter-spacing: 0.005em; transform-origin: left top;">NOSTALGIA VOL. 01</span>
        </h3>
      </div>
      <div class="flex flex-col gap-2.5 min-w-0">
        <p class="text-fg-dim max-w-[52ch] text-pretty m-0 text-[15px] leading-[1.5]">Film emulation LUTs &amp; PowerGrades built on real Kodak Vision3 ARRISCAN scans. Drop in. Get the look.</p>
        <p class="text-fg-dim max-w-[52ch] text-pretty m-0 text-[15px] leading-[1.5]">Each pack ships with DaVinci Resolve PowerGrades, 33-point .cube LUTs, and a halation + grain node tree. Used by creators and studios in 20+ countries.</p>
        <a class="self-start inline-flex items-center gap-2.5 mt-2 px-5 py-3 bg-yellow text-ink border-2 border-red font-mono text-xs tracking-[0.14em] uppercase font-medium transition-transform duration-200 hover:-translate-x-[3px] hover:-translate-y-[3px] shop-cta-hover"
           href="https://jameskirbynostalgia.gumroad.com/" target="_blank" rel="noopener noreferrer">
          Shop all →
        </a>
      </div>
    </div>

    <div class="products grid grid-cols-3 gap-4 items-stretch reveal">
      {products.map((p) => (
        <a class="product border border-rule bg-ink-2 flex flex-col transition-transform duration-300 min-w-0 w-full hover:-translate-y-1 hover:border-yellow"
           href="https://jameskirbynostalgia.gumroad.com/" target="_blank" rel="noopener noreferrer">
          <div class="aspect-[4/5] relative overflow-hidden shrink-0">
            <div class="absolute inset-0" style={`background: ${p.gradient};`}></div>
            <div class="absolute top-3.5 left-3.5 font-display text-[22px] text-yellow leading-none">KIRBY</div>
            <div class="absolute bottom-3.5 left-3.5 right-3.5 flex justify-between font-mono text-[10px] tracking-[0.12em] uppercase text-cream">
              <span>{p.lot}</span><span>{p.stock}</span>
            </div>
          </div>
          <div class="p-5 flex flex-col gap-3.5 border-t border-rule grow">
            <div class="font-display text-[22px] leading-none uppercase text-yellow min-h-[22px] prod-title-shadow">{p.title}</div>
            <p class="text-fg-dim text-[13px] m-0 text-pretty leading-[1.5] grow">{p.desc}</p>
            <div class="flex justify-between items-center pt-3.5 border-t border-dashed border-rule font-mono text-xs tracking-[0.08em] uppercase mt-auto">
              <span class="text-yellow font-medium">{p.price}</span>
              <span class="text-cream buy-text">Buy →</span>
            </div>
          </div>
        </a>
      ))}
    </div>
  </div>
</section>

<script>
  function fitShopLockup() {
    document.querySelectorAll('.shop-lockup').forEach(lockup => {
      const big = lockup.querySelector('.line:not(.line-small)') as HTMLElement | null;
      const small = lockup.querySelector('.line-small') as HTMLElement | null;
      if (!big || !small) return;
      small.style.transform = 'scale(1)';
      small.style.height = '';
      const bigW = big.scrollWidth || big.getBoundingClientRect().width;
      const smallW = small.scrollWidth || small.getBoundingClientRect().width;
      if (smallW > 0 && bigW > 0) {
        small.style.transform = `scale(${bigW / smallW})`;
        small.style.height = small.getBoundingClientRect().height + 'px';
      }
    });
  }
  fitShopLockup();
  window.addEventListener('resize', fitShopLockup);
  if (document.fonts?.ready) document.fonts.ready.then(fitShopLockup);
</script>

<style>
.shop-head { grid-template-columns: 1.1fr 1fr; }
@media (max-width: 820px) { .shop-head { grid-template-columns: 1fr; gap: 24px; align-items: start; } }

.shop-lockup { font-size: clamp(64px, 8.5vw, 120px); line-height: 0.88; text-shadow: 0 0.08em 0 var(--color-red); }

.shop-cta-hover:hover { box-shadow: 5px 5px 0 var(--color-red); }

.prod-title-shadow { text-shadow: 0 0.06em 0 var(--color-red); }

@media (max-width: 900px) { .products { grid-template-columns: repeat(2, 1fr); } }
@media (max-width: 540px)  { .products { grid-template-columns: 1fr; } }

.product:hover .buy-text { color: var(--color-yellow); }
</style>
```

- [ ] **Step 2: Verify shop section — lockup scaling, product cards, hover states.**

- [ ] **Step 3: Commit**

```bash
git add src/components/Shop.astro
git commit -m "feat: migrate Shop to Tailwind utilities"
```

---

## Task 10: Work.astro

**Files:**
- Modify: `src/components/Work.astro`

- [ ] **Step 1: Replace with utility-first version**

```astro
---
const reels = [
  { href: "https://www.instagram.com/p/DU_y7hdkyrA/", src: "https://www.instagram.com/p/DU_y7hdkyrA/embed/?rd=https%3A%2F%2Fwww.instagram.com", title: "Personal short film — colour grade, Chiang Mai", label: "01 · Personal" },
  { href: "https://www.instagram.com/p/DUw9EgzEhbW/", src: "https://www.instagram.com/p/DUw9EgzEhbW/embed/", title: "Personal reel — colour grade", label: "02 · Personal" },
  { href: "https://www.instagram.com/p/DUsZhleksWo/", src: "https://www.instagram.com/p/DUsZhleksWo/embed/", title: "Personal reel — colour grade", label: "03 · Personal" },
  { href: "https://www.instagram.com/reel/DWqm7ZhEQyu/", src: "https://www.instagram.com/reel/DWqm7ZhEQyu/embed/", title: "CMD Careers — short-form content reel", label: "04 · CMD Careers" },
  { href: "https://www.instagram.com/reel/DTK_xIYCNiS/", src: "https://www.instagram.com/reel/DTK_xIYCNiS/embed/", title: "CMD Careers — short-form reel", label: "05 · CMD Careers" },
  { href: "https://www.instagram.com/p/DUlKvP1knrD/", src: "https://www.instagram.com/p/DUlKvP1knrD/embed/", title: "Cassie Schoonover — colour grade", label: "06 · Cassie Schoonover" },
  { href: "https://www.instagram.com/p/DU0xfx8En9M/", src: "https://www.instagram.com/p/DU0xfx8En9M/embed/", title: "Cassie Schoonover — reel", label: "07 · Cassie Schoonover" },
  { href: "https://www.instagram.com/reel/DSzgejCjeM-/", src: "https://www.instagram.com/reel/DSzgejCjeM-/embed/", title: "CMD Careers — short-form reel", label: "08 · CMD Careers" },
];
---
<section class="section-pad" id="work">
  <div class="section-head reveal">
    <div>
      <div class="label">§ 01 / Selected work</div>
      <h2>The <span class="italic-serif">work</span>.</h2>
    </div>
    <p>Recent reels, grades and shorts — mine, CMD Careers', and clients'. All graded in DaVinci Resolve on custom KIRBY PowerGrades, Kodak Vision3 underneath.</p>
  </div>

  <div class="grid grid-cols-12 gap-4">
    {reels.map((r) => (
      <a class="tile reel-v relative bg-black overflow-hidden border border-rule aspect-[9/16] transition-transform duration-[350ms] hover:-translate-y-1 hover:border-yellow reveal"
         href={r.href} target="_blank" rel="noopener noreferrer">
        <div class="absolute inset-0 overflow-hidden">
          <iframe
            title={r.title}
            src={r.src}
            loading="lazy"
            allowtransparency="true"
            allowfullscreen="true"
            class="tile-iframe"
          ></iframe>
        </div>
        <span class="absolute top-2.5 left-2.5 z-[3] font-mono text-[9px] tracking-[0.18em] uppercase text-yellow bg-[rgba(18,9,5,0.7)] px-2 py-1 pointer-events-none">{r.label}</span>
      </a>
    ))}
  </div>
</section>

<style>
.reel-v { grid-column: span 3; }
@media (max-width: 900px) { .reel-v { grid-column: span 6; } }
@media (max-width: 540px)  { .reel-v { grid-column: span 12; aspect-ratio: 16/10; } }

.tile-iframe {
  position: absolute;
  top: -60px; left: -1px;
  width: calc(100% + 2px);
  height: calc(100% + 180px);
  border: 0;
  background: #000;
}
</style>
```

- [ ] **Step 2: Verify work grid — 4-across on desktop, 2-across tablet, full-width mobile. Tiles hover lift.**

- [ ] **Step 3: Commit**

```bash
git add src/components/Work.astro
git commit -m "feat: migrate Work to Tailwind utilities"
```

---

## Task 11: Hero.astro

**Files:**
- Modify: `src/components/Hero.astro`

- [ ] **Step 1: Replace with utility-first version**

```astro
---
---
<section class="hero relative overflow-hidden flex flex-col justify-end min-h-screen" id="top">
  <div class="hero-inner relative z-[2] grow shrink-0 grid gap-12 items-center pt-6">
    <div>
      <p class="hero-eyebrow font-serif italic font-normal text-yellow tracking-[-0.02em] leading-[0.9] m-0">James</p>
      <h1 class="hero-title font-display text-yellow leading-none tracking-[0.005em] m-0 uppercase word-break-normal overflow-wrap-break-word">KIRBY<span class="sr-only"> — Video Editor &amp; Colourist, Chiang Mai</span></h1>
      <p class="hero-sub font-mono tracking-[0.3em] uppercase text-yellow mt-[18px]"><b class="text-yellow font-medium">Content creator</b> · Video editor · Colourist</p>
    </div>
    <div class="hero-portrait aspect-[4/5] border-2 border-yellow relative overflow-hidden max-w-full">
      <span class="absolute top-3 left-3 font-mono text-[9px] tracking-[0.2em] uppercase text-yellow">FRAME 01</span>
      <span class="absolute bottom-3 left-3 font-mono text-[9px] tracking-[0.2em] uppercase text-fg-mute">[PHOTO / JAMES]</span>
      <span class="absolute bottom-3 right-3 font-mono text-[9px] tracking-[0.2em] uppercase text-fg-mute">35MM</span>
    </div>
  </div>
  <div class="relative z-[2] flex justify-between items-end border-t border-rule pt-5 mt-10 font-mono text-[11px] tracking-[0.14em] uppercase text-fg-dim">
    <div>Chiang Mai · Worldwide · 2026</div>
    <div class="text-yellow">↓ The work</div>
  </div>
</section>

<style>
.hero {
  padding: calc(32px + 49px + 48px) clamp(20px, 4vw, 64px) 5vh;
  background: radial-gradient(120% 80% at 50% 35%, #3a1a0a 0%, #1a0d05 55%, #0a0503 100%);
}
.hero::before {
  content: "";
  position: absolute; inset: 0;
  background: repeating-linear-gradient(to bottom, transparent 0 3px, rgba(0,0,0,0.12) 3px 4px);
  opacity: 0.5;
  pointer-events: none;
}
.hero-inner { grid-template-columns: 1fr minmax(240px, 380px); }
@media (max-width: 1024px) { .hero-inner { grid-template-columns: 1fr minmax(220px, 320px); gap: 32px; } }
@media (max-width: 760px)  { .hero-inner { grid-template-columns: 1fr; } }

.hero-eyebrow { font-size: clamp(80px, 13vw, 225px); margin-bottom: -0.04em; text-shadow: 0 0.06em 0 var(--color-red); }
.hero-title   { font-size: clamp(88px, 15vw, 245px); text-shadow: 0 0.08em 0 var(--color-red); }
.hero-sub     { font-size: clamp(11px, 1vw, 14px); }

.hero-portrait {
  background: linear-gradient(160deg, #3a1a0a 0%, #1a0d05 100%);
  box-shadow: 8px 8px 0 var(--color-red);
}
.hero-portrait::before {
  content: "";
  position: absolute; inset: 0;
  background: repeating-linear-gradient(45deg, rgba(252,175,23,0.05) 0 10px, transparent 10px 22px);
}
</style>
```

- [ ] **Step 2: Verify hero renders — full viewport height, large type, portrait box with offset shadow, bottom metadata bar.**

- [ ] **Step 3: Commit**

```bash
git add src/components/Hero.astro
git commit -m "feat: migrate Hero to Tailwind utilities"
```

---

## Task 12: About.astro

**Files:**
- Modify: `src/components/About.astro`

- [ ] **Step 1: Replace with utility-first version**

```astro
---
---
<section class="section-pad bg-ink-2 border-y border-rule" id="about">
  <div class="about grid gap-[60px] items-start">
    <div class="portrait aspect-[4/5] border-2 border-yellow relative overflow-hidden reveal">
      <span class="absolute top-4 right-4 font-mono text-[10px] tracking-[0.16em] uppercase text-yellow">35MM · PORTRAIT</span>
      <span class="absolute bottom-4 left-4 font-mono text-[10px] tracking-[0.16em] uppercase text-fg-mute">[PHOTO / JAMES — CHIANG MAI]</span>
    </div>
    <div class="reveal">
      <div class="label">§ 06 / About</div>
      <h2 class="about-h2 font-display leading-none mt-4 mb-7 uppercase text-yellow">Raised on <span class="italic-serif">Kodak</span>.<br />Built for frames<br />that move.</h2>
      <p class="about-p text-fg-dim leading-[1.55] text-pretty max-w-[52ch]">I'm James. A content creator who also colour grades the videos I make at CMD Careers. Half-Thai, half-British. Twenty-two. Based in Chiang Mai.</p>
      <p class="about-p2 font-serif italic font-normal text-fg-dim max-w-[54ch] leading-[1.5] mt-[18px] text-pretty">On the side I run KIRBY, a small product line of film emulation LUTs and PowerGrades used by creators and studios in 20+ countries. Everything is built on Kodak Vision3 colour science and finished in DaVinci Resolve. If your brand deserves better frames, book me on Upwork.</p>
    </div>
  </div>
</section>

<style>
.about { grid-template-columns: 1fr 1.2fr; }
@media (max-width: 900px) { .about { grid-template-columns: 1fr; } }

.portrait {
  background: linear-gradient(160deg, #3a1a0a 0%, #1a0d05 100%);
  box-shadow: 10px 10px 0 var(--color-red);
}
.portrait::before {
  content: "";
  position: absolute; inset: 0;
  background: repeating-linear-gradient(45deg, rgba(252,175,23,0.05) 0 10px, transparent 10px 22px);
}

.about-h2 { font-size: clamp(44px, 6vw, 80px); text-shadow: 0 0.08em 0 var(--color-red); }
.about-p  { font-size: clamp(16px, 1.2vw, 19px); }
.about-p2 { font-size: clamp(18px, 1.4vw, 22px); }
</style>
```

- [ ] **Step 2: Verify about section — two-column grid, portrait box, copy typography.**

- [ ] **Step 3: Commit**

```bash
git add src/components/About.astro
git commit -m "feat: migrate About to Tailwind utilities"
```

---

## Task 13: Nav.astro

**Files:**
- Modify: `src/components/Nav.astro`

- [ ] **Step 1: Replace with utility-first version**

```astro
---
---
<nav class="fixed inset-x-0 z-[100] flex items-center justify-between font-mono text-[11px] tracking-[0.14em] uppercase text-cream bg-[rgba(18,9,5,0.88)] backdrop-blur-sm border-b border-rule" style="top: 32px;" aria-label="Primary">
  <a href="#top" class="mark font-display text-[24px] text-yellow tracking-[-0.02em] uppercase">JK</a>
  <ul class="list-none flex gap-7 m-0 p-0 nav-links">
    <li><a href="#work" class="hover:text-yellow">Work</a></li>
    <li><a href="#vsl" class="hover:text-yellow">Reel</a></li>
    <li><a href="#cases" class="hover:text-yellow">Cases</a></li>
    <li><a href="#services" class="hover:text-yellow">Services</a></li>
    <li><a href="#shop" class="hover:text-yellow">Shop</a></li>
    <li><a href="#about" class="hover:text-yellow">About</a></li>
  </ul>
  <div class="flex items-center gap-4">
    <a class="cta px-3.5 py-[9px] bg-yellow text-ink border-2 border-red font-medium transition-transform duration-150 hover:-translate-x-0.5 hover:-translate-y-0.5 nav-cta-desk"
       href="https://www.upwork.com/freelancers/~01507e9897388a0aed?mp_source=share" target="_blank" rel="noopener noreferrer">Hire on Upwork →</a>
    <button class="hamburger hidden flex-col justify-center gap-[5px] w-9 h-9 bg-transparent border border-rule-strong cursor-pointer p-2" aria-label="Open menu" aria-expanded="false" aria-controls="mobile-menu" type="button">
      <span class="block w-full h-0.5 bg-cream transition-transform duration-200"></span>
      <span class="block w-full h-0.5 bg-cream transition-opacity duration-200"></span>
      <span class="block w-full h-0.5 bg-cream transition-transform duration-200"></span>
    </button>
  </div>
</nav>

<div class="mobile-menu fixed inset-x-0 z-[99] bg-[rgba(18,9,5,0.96)] backdrop-blur-md border-b-2 border-red flex-col gap-4 hidden" id="mobile-menu" aria-hidden="true" style="top: calc(32px + 49px);">
  <ul class="list-none m-0 p-0 flex flex-col">
    <li><a href="#work" class="block py-3.5 font-mono text-xs tracking-[0.18em] uppercase text-cream border-b border-rule hover:text-yellow">Work</a></li>
    <li><a href="#vsl" class="block py-3.5 font-mono text-xs tracking-[0.18em] uppercase text-cream border-b border-rule hover:text-yellow">Reel</a></li>
    <li><a href="#cases" class="block py-3.5 font-mono text-xs tracking-[0.18em] uppercase text-cream border-b border-rule hover:text-yellow">Cases</a></li>
    <li><a href="#services" class="block py-3.5 font-mono text-xs tracking-[0.18em] uppercase text-cream border-b border-rule hover:text-yellow">Services</a></li>
    <li><a href="#shop" class="block py-3.5 font-mono text-xs tracking-[0.18em] uppercase text-cream border-b border-rule hover:text-yellow">Shop</a></li>
    <li><a href="#about" class="block py-3.5 font-mono text-xs tracking-[0.18em] uppercase text-cream border-b border-rule hover:text-yellow">About</a></li>
  </ul>
  <a class="self-start inline-block mt-2 px-5 py-3 bg-yellow text-ink border-2 border-red font-mono text-xs tracking-[0.14em] uppercase font-medium"
     href="https://www.upwork.com/freelancers/~01507e9897388a0aed?mp_source=share" target="_blank" rel="noopener noreferrer">Hire on Upwork →</a>
</div>

<script>
  const btn = document.querySelector('.hamburger') as HTMLButtonElement;
  const menu = document.getElementById('mobile-menu')!;
  btn.addEventListener('click', () => {
    const open = btn.getAttribute('aria-expanded') === 'true';
    btn.setAttribute('aria-expanded', String(!open));
    btn.setAttribute('aria-label', open ? 'Open menu' : 'Close menu');
    menu.setAttribute('aria-hidden', String(open));
    menu.classList.toggle('open', !open);
    btn.classList.toggle('active', !open);
  });
  menu.querySelectorAll('a').forEach(link => {
    link.addEventListener('click', () => {
      btn.setAttribute('aria-expanded', 'false');
      btn.setAttribute('aria-label', 'Open menu');
      menu.setAttribute('aria-hidden', 'true');
      menu.classList.remove('open');
      btn.classList.remove('active');
    });
  });
</script>

<style>
nav { padding: 14px clamp(20px, 4vw, 64px); }
.mobile-menu { padding: 24px clamp(20px, 4vw, 64px); }
.mobile-menu.open { display: flex; }

.mark { text-shadow: 0 0.06em 0 var(--color-red); }
.nav-cta-desk:hover { box-shadow: 3px 3px 0 var(--color-red); color: var(--color-ink); }

.hamburger.active span:nth-child(1) { transform: translateY(7px) rotate(45deg); }
.hamburger.active span:nth-child(2) { opacity: 0; }
.hamburger.active span:nth-child(3) { transform: translateY(-7px) rotate(-45deg); }

@media (max-width: 820px) {
  .nav-links { display: none; }
  .hamburger { display: flex; }
  .nav-cta-desk { display: none; }
}
</style>
```

- [ ] **Step 2: Verify nav — fixed position, logo, desktop links visible, hamburger hidden on desktop, hamburger visible on mobile, mobile menu opens/closes, CTA button.**

- [ ] **Step 3: Commit**

```bash
git add src/components/Nav.astro
git commit -m "feat: migrate Nav to Tailwind utilities"
```

---

## Task 14: Build verification + push

**Files:** none modified

- [ ] **Step 1: Run production build**

```bash
cd /Users/antarikshakumar/Downloads/JK/KIRBY-v6-astro
npm run build 2>&1
```

Expected: exits with code 0, `dist/` populated, no CSS errors.

- [ ] **Step 2: Check CSS bundle size**

```bash
wc -c dist/_astro/*.css
gzip -c dist/_astro/*.css | wc -c
```

Expected: raw ≤ 40KB, gzipped ≤ 9KB.

- [ ] **Step 3: Preview build**

```bash
npm run preview
```

Open `http://localhost:4321` — verify all 8 sections render visually identical to pre-migration.

- [ ] **Step 4: Push to GitHub**

```bash
git push
```

Expected: all 14 commits pushed to `antutroll27/kirby-v6-astro`.
