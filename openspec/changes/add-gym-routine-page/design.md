# Design

## Context

Greenfield: the repo holds only `PROMPT.md`, `design.png`, `styleguide.md` and OpenSpec scaffolding. Precedence when sources conflict: `PROMPT.md` > `design.png` (layout) > `styleguide.md`. Requirements live in `specs/`; this document covers how to meet them in one hand-written `index.html`.

Main tension: the style guide's generous spacing vs. the hard "no vertical scroll at 1280×720" requirement with up to 7 exercises × 2 lines per column plus a per-column note line.

## Goals / Non-Goals

**Goals:**
- One readable `index.html` (~one screen of data, then CSS, then JS) that the owner can edit by hand.
- Content correct without JS; JS only personalizes (day, status, marking, theme, quotes).
- Zero layout shift from the hidden theme button or pen marks.

**Non-Goals:**
- No persistence of any kind (no storage, no cookies).
- No automated test harness; verification is manual in a browser at the listed viewport sizes.
- No service worker / offline support, no analytics.

## Decisions

### 1. Hardcoded markup + `routine` object as the edit source
The four `<section>`s are written in HTML (no-JS fallback). The `routine` object sits at the top of the script with a comment: "Edit here AND in the markup below — keep in sync." JS reads `routine[id].name`/`focus` only for the status line. *Alternative:* render from JS + `<noscript>` copy — rejected by the prompt's stated preference and it duplicates markup anyway.

### 2. Today marking via attribute, not DOM rebuilding
JS sets `data-today="true"` on one section (removing it from others) and toggles a visually hidden `<span class="sr-only"> — today</span>` inside its `<h2>`. Each `<h2>` contains the heading text wrapped in a `position: relative` span holding two pre-rendered underline SVGs: an ink one (shown by default, thin) and a green one (shown only under `[data-today="true"]`). Re-marking on midnight rollover just moves the attribute.

### 3. Mobile "today first" via CSS `order`
Below 600px the grid becomes a single column and `[data-today="true"] { order: -1; }`. DOM order is unchanged, so reading order for assistive tech remains the schedule order on desktop. *Alternative:* move the DOM node in JS — rejected; it complicates resize handling and the no-JS state.

### 4. Desktop height budget
- `html, body { height: 100%; }`; `body` is a flex column: header (headline + status), `main` (grid, `flex: 1 1 auto; min-height: 0`), footer ("Tired?").
- Vertical spacers use `clamp(1rem, 6vh, 6rem)` (top) and `clamp(1rem, 5vh, 4rem)` (between blocks) instead of `--space-3xl`/`--space-2xl` at ≥ 900px.
- Exercise gap `--space-sm`–`--space-md` via `clamp(0.5rem, 1.6vh, 1rem)`.
- `--fs-body` on desktop: `clamp(1rem, 1.1vw + 0.2vh, 1.3rem)`; `--fs-display` reduced via a height-aware clamp like `clamp(2.25rem, min(6vw, 8vh), 4.5rem)` if 1280×720 still overflows.
- Worst case is Upper A/Upper B (7 items). Verify with `document.documentElement.scrollHeight <= innerHeight` in DevTools at each size and both themes (night has the optional hairline above the headline — budget for it or drop it if tight).
- At < 900px, `html, body` height is `auto`/min-height so pages scroll naturally and the style-guide spacing applies.

### 5. Theme: pre-paint inline script + runtime controller
A tiny `<script>` in `<head>` before the stylesheet computes `h = new Date().getHours()` and sets `data-theme` (`dark` if `h >= 19 || h < 7`). CSS defines day tokens on `:root` and night tokens on `:root[data-theme="dark"]` only — no `prefers-color-scheme` media query at all. The main script keeps `manualTheme` (null | "light" | "dark") and `lastAutoTheme`. On each tick (60 s `setInterval` + `visibilitychange` when visible): compute auto theme; if it differs from `lastAutoTheme`, a boundary was crossed → clear `manualTheme`; apply `manualTheme ?? auto`. Clicking the "o" sets `manualTheme` to the opposite of the currently applied theme.

### 6. Hidden "o" button and accessible headline
```html
<h1>
  <span aria-hidden="true">T<button class="theme-o" aria-label="Switch color theme">o</button>day is <span class="day-mark">Monday<svg …circle/></span></span>
  <span class="sr-only">Today is Monday</span>
</h1>
```
Problem: an `aria-hidden` subtree hides the button from AT too, and focusable content inside `aria-hidden` is an a11y error. Resolution: keep the button **outside** the hidden span's semantics by structuring as `<span aria-hidden="true">T</span><button …>o</button><span aria-hidden="true">day is …</span><span class="sr-only">Today is Monday</span>`. To keep the heading's accessible name exactly "Today is Monday" (rather than "Switch color theme Today is Monday"), the `<h1>` gets `aria-labelledby` pointing at the `sr-only` span's id; the button stays focusable and announced as its own control; the visible text is pure inline runs with no whitespace between them so there is no gap. Button CSS: `all: unset; font: inherit; color: inherit; letter-spacing: inherit; cursor: pointer; position: relative;` with `::after { content:""; position:absolute; inset: 50% auto auto 50%; width:44px; height:44px; transform: translate(-50%,-50%); }` for the tap area, and `:focus-visible { outline: 2px solid var(--pen); outline-offset: 4px; border-radius: 2px; }`. Because `all: unset` removes the `display`, set `display: inline`. Without JS the button still exists but does nothing — acceptable; alternatively hide it until JS adds a `js` class to `<html>` and render plain "Today’s workouts" for no-JS (the no-JS headline text differs anyway, see decision 7).

### 7. No-JS headline
Markup ships with the visible headline "Today’s workouts" (no circle, no button) in a `.headline-fallback` and the JS headline structure in a `.headline-js` block hidden by default; the head script adds `class="js"` to `<html>` and CSS swaps visibility (`.js .headline-fallback {display:none}`). The status line is empty/hidden without JS. This avoids a dead toggle button for no-JS users and needs no `<noscript>`.

### 8. Pen marks
Inline SVGs with `preserveAspectRatio="none"`, `vector-effect: non-scaling-stroke`, stroke 2.5 (day) / 2 (night). The headline mark is a slightly tilted loop that goes round ~1¼ times and flicks out at the top right (Sequoia-style), placed around today's routine name. Non-scaling strokes dash in screen pixels, so `pathLength="1"` doesn't work: JS measures each visible path's on-screen length into `--len` and the dash animation uses that. Circle box: `inset: -35% -12%`; underline: below baseline, `right: -5%`. Load animation is gated by a `.animate` class on `<html>` added once at startup (circle `700ms`, underline `400ms` with `animation-delay: 850ms`). Midnight rollover removes `.animate` before updating so marks render fully drawn. Reduced motion: `animation: none; stroke-dashoffset: 0`, and `transition: none` everywhere.

### 9. Quote dialog
Native `<dialog>` + `showModal()`. Backdrop close: on `click`, if `event.target === dialog` (content wrapped in an inner `div` that fills the padding box so clicks on padding don't count as backdrop). Esc is native (`cancel` event). On `close`, call `tiredBtn.focus()` (native restoration exists in modern browsers but is explicit here for consistency). Index: `i = Math.floor(Math.random() * quotes.length)` at load; on open, show `quotes[i]` then `i = (i + 1) % quotes.length`. Backdrop: `::backdrop { background: rgb(249 245 238 / .7); background: color-mix(in srgb, var(--paper) 70%, transparent); }` — note `::backdrop` doesn't inherit custom properties in older browsers, so the fallback uses literal values and dark override via `[data-theme="dark"] dialog::backdrop`. Fade-in via `@keyframes` on `dialog[open]`, disabled under reduced motion.

### 10. Weekday resolution
`resolveDay()` returns `Number(param)` if `/^[0-6]$/` matches `URLSearchParams.get("day")`, else `new Date().getDay()`. Rendering is idempotent: `applyDay(d)` updates text, attributes and status; it's called on load and in the tick only when `d` changed.

## Risks / Trade-offs

- [Desktop overflow at 1280×720 with 7-item columns plus a note line] → height-based clamps on spacing, display size and item gap; verify each size in both themes; drop the night-mode hairline at short heights if needed.
- [Hidden "o" button reads oddly to screen readers] → separate `sr-only` heading text and an explicit `aria-label`; tested with VoiceOver rotor.
- [`::backdrop` and `color-mix` support] → literal rgba fallback declared first.
- [`setInterval` throttled in background tabs] → `visibilitychange` re-check covers the return-to-tab case.
- [Sketch shows underlines under every heading] → non-today headings get a thin ink underline (style guide permits it); today's is green. Easy to remove later.
- [Routine object and markup drift apart] → comment at both locations; the object only drives the status line, so drift is visible quickly.

## Migration Plan

New site; no migration. Deploy: commit `index.html` and `.nojekyll` to `main`, then GitHub → Settings → Pages → Source: "Deploy from a branch" → Branch `main`, folder `/ (root)` → Save. Rollback: revert the commit or unpublish Pages.
