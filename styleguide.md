# Style Guide — Gym Routine Page

A single static page that tells me what to train today and lists the exercises for each of my four workouts (Upper A, Lower A, Upper B, Lower B). The look is borrowed from two references:

- **Day mode (default) — Sequoia.** Warm cream paper, near-black serif type, and loose, hand-drawn green pen marks that circle and underline things like someone annotated the page with a marker.
- **Night mode — Blackstone.** Pure black background, white type, the same quiet serif headlines, a light sans for small text, lots of empty space.

The page should feel like a well-set printed card, not an app. Typography does almost all of the work. The only "decoration" is the green pen.

---

## 1. Design principles

1. **Type is the design.** No cards, no shadows, no icons, no gradients, no photos. Hierarchy comes from size, weight and space.
2. **Spend the boldness in one place: the green pen.** Hand-drawn circles and underlines mark *today's* workout and nothing else of importance. If everything is circled, nothing is.
3. **Generous emptiness.** Both references leave huge margins around short lines of text. When unsure, add space rather than a border.
4. **Readable at the gym.** I'll glance at this on my phone between sets, so exercise names must be large enough to read at arm's length and sets/reps must be easy to scan.
5. **Calm motion.** One moment of motion on load (the pen drawing itself). Nothing else moves unless I tap it.

---

## 2. Color

All colors are CSS custom properties on `:root`. Every text color and the surface behind it must come from these tokens.

### Day mode (Sequoia) — default

| Token | Hex | Use |
|---|---|---|
| `--paper` | `#F9F5EE` | Page background (warm cream) |
| `--ink` | `#1A1814` | Headlines and body text (warm near-black, reads as ink on paper) |
| `--ink-soft` | `#5E5A52` | Secondary text: sets × reps, notes, footnotes |
| `--rule` | `#D9D2C4` | Hairline dividers, if needed at all |
| `--pen` | `#0E9A6A` | The green pen: circles, underlines, focus rings, link hover |
| `--pen-wash` | `#0E9A6A1A` | Optional very faint green behind today's column (10% alpha) |

### Night mode (Blackstone)

| Token | Hex | Use |
|---|---|---|
| `--paper` | `#000000` | True black background (Blackstone uses pure black, not charcoal) |
| `--ink` | `#FFFFFF` | Headlines and body text |
| `--ink-soft` | `#A3A3A3` | Secondary text |
| `--rule` | `#2A2A2A` | Hairline dividers |
| `--pen` | `#3FCF97` | Green pen, lifted for contrast on black (keep the green — it's the personality) |
| `--pen-wash` | `#3FCF971F` | Faint highlight behind today's column |

### Rules

- Never introduce another accent color. Green is the only color on the page.
- `--pen` is for marks and interaction only, never for body text or long runs of copy.
- Contrast: `--ink` on `--paper` and `--ink-soft` on `--paper` must both pass WCAG AA (4.5:1) in both modes. The values above do.

---

## 3. Typography

### Typefaces

Both reference sites use proprietary fonts. These free Google Fonts are close stand-ins:

| Role | Font | Fallback stack | Why |
|---|---|---|---|
| **Serif** (headline, workout names, exercise names) | **Source Serif 4** (variable, has optical sizes) | `"Source Serif 4", "Iowan Old Style", "Georgia", serif` | Matches Sequoia's calm, bookish text serif; at display optical size it also gets close to Blackstone's refined headline serif |
| **Sans** (small text: sets × reps, footer, toggle) | **Figtree** | `"Figtree", "Helvetica Neue", Arial, sans-serif` | Clean, slightly warm, works in a light weight like Blackstone's body copy |

Load via:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Source+Serif+4:ital,opsz,wght@0,8..60,300..700;1,8..60,300..700&family=Figtree:wght@300;400;500&display=swap" rel="stylesheet">
```

Day mode leans on the serif almost exclusively (like Sequoia). Night mode uses the same serif for headlines but lets the light sans carry more of the small text (like Blackstone).

### Type scale

Fluid sizes with `clamp()` so the same scale works on desktop and mobile. Based on a ~1.333 (perfect fourth) ratio.

| Token | Size | Font / weight | Line height | Letter spacing | Use |
|---|---|---|---|---|---|
| `--fs-display` | `clamp(2.5rem, 6vw, 4.5rem)` | Serif 400, opsz 60 | 1.05 | `-0.02em` | "Today is Monday" |
| `--fs-h2` | `clamp(1.5rem, 2.6vw, 2rem)` | Serif 400 | 1.2 | `-0.01em` | Workout names: Upper A, Lower A… |
| `--fs-body` | `clamp(1.125rem, 1.6vw, 1.3rem)` | Serif 400 | 1.5 | `0` | Exercise names |
| `--fs-small` | `0.9375rem` | Sans 400 (day) / 300 (night) | 1.45 | `0.01em` | Sets × reps, notes |
| `--fs-micro` | `0.8125rem` | Sans 400 | 1.4 | `0.01em` | Footer text, theme toggle |

### Rules

- **Sentence case everywhere.** No ALL CAPS labels, no tracked-out eyebrows.
- **Weights stay light.** 400 for serif text, 300–400 for sans. Bold (600) is allowed only on a single word in night mode if it echoes Blackstone's "**Build** with" lockup — use at most once, or not at all.
- **No italics for emphasis.** Emphasis comes from the green pen.
- Exercise list line length: under ~40 characters per line in columns; that's naturally enforced by the four-column layout.
- Use real typographic characters: `×` for sets × reps (`3 × 8`), `’` for apostrophes, `–` for ranges (`8–10`).
- Numerals: use `font-variant-numeric: lining-nums tabular-nums;` on sets × reps so numbers line up down a column.

---

## 4. Layout

Based on my sketch. Content is **centered** on the page as a block; text inside each column is **left-aligned**.

### Desktop (≥ 900px)

```
┌───────────────────────────────────────────────────────────────┐
│                                                               │
│                     Today is ‹Monday›                         │  ← centered display serif
│                                                               │
│                                                               │
│   Upper A:        Lower A:         Upper B:        Lower B:   │  ← 4 equal columns
│   ─────────       ─────────        ─────────       ─────────  │
│   Bench press     Squat            ...             ...        │
│   3 × 8           4 × 6                                       │
│   Row             RDL                                         │
│   3 × 10          3 × 8                                       │
│   ...             ...                                         │
│                                                               │
│                                                               │
│                           Tired?                              │  ← centered, small
│                                                               │
└───────────────────────────────────────────────────────────────┘
```

- Max content width: `72rem` (1152px), centered with `margin-inline: auto`.
- Horizontal page padding: `clamp(1.5rem, 5vw, 4rem)`.
- Columns: `display: grid; grid-template-columns: repeat(4, 1fr); column-gap: clamp(2rem, 4vw, 4rem);`
- Space between headline and columns: `--space-2xl`.
- Space between columns and "Tired?": `--space-2xl`.
- Top of page to headline: at least `--space-3xl` — the headline should sit well down the page, like Sequoia's single line floating in cream.

### Tablet (600–899px)

Two columns × two rows (Upper A / Lower A on top, Upper B / Lower B below).

### Mobile (< 600px)

```
┌──────────────────┐
│                  │
│   Today is       │
│   Monday         │
│                  │
│   Upper A:       │
│   ────────       │
│   Bench press    │
│   3 × 8          │
│   ...            │
│                  │   ↓ scroll
│   Lower A:       │
│   ...            │
│                  │
│   Upper B:       │
│   ...            │
│                  │
│   Lower B:       │
│   ...            │
│                  │
│      Tired?      │
└──────────────────┘
```

- Single column, stacked in the same order as desktop.
- **Today's workout moves to the top** of the stack on mobile (directly under the headline) so I don't have to scroll to find it. Others follow in their normal order.
- Headline left-aligned on mobile; centered on desktop.
- Use `height: 100%` on `html, body` rather than `100vh`.

### Spacing scale

| Token | Value |
|---|---|
| `--space-xs` | `0.25rem` |
| `--space-sm` | `0.5rem` |
| `--space-md` | `1rem` |
| `--space-lg` | `1.5rem` |
| `--space-xl` | `2.5rem` |
| `--space-2xl` | `clamp(3rem, 8vw, 6rem)` |
| `--space-3xl` | `clamp(4rem, 14vw, 10rem)` |

---

## 5. Components

### 5.1 Headline — "Today is ‹day›"

- `<h1>`, display serif, `--ink`.
- The day name is filled in by JavaScript from `new Date().getDay()`.
- The **day name gets a hand-drawn green circle** (see 5.4), like "daring" in the Sequoia reference. The words "Today is" get nothing.
- If today is a rest day, the headline reads "Today is Sunday" and a small line under it in `--fs-small` / `--ink-soft` says "Rest day." — no circle on any column.

### 5.2 Workout column

```html
<section class="workout" data-today="true" aria-labelledby="upper-a">
  <h2 id="upper-a">Upper A</h2>
  <ol class="exercises">
    <li>
      <span class="exercise-name">Bench press</span>
      <span class="exercise-detail">3 × 8</span>
    </li>
  </ol>
</section>
```

- Heading: `--fs-h2`, serif, followed by a colon in the visible text only if I want to match the sketch ("Upper A:"). Keep it consistent across all four.
- Under each heading: a short hand-drawn underline in `--ink` (thin, 1.25px) for non-today columns — **or** skip it entirely for a cleaner look. Today's column gets the green version (5.4).
- Exercise list: no bullets, no numbers (`list-style: none`). Each item:
  - `.exercise-name` — serif, `--fs-body`, `--ink`, on its own line.
  - `.exercise-detail` — sans, `--fs-small`, `--ink-soft`, directly beneath. Tabular numerals.
  - Gap between items: `--space-md`.
- **Today's column** (`data-today="true"`):
  - Heading gets the green hand-drawn underline that sweeps a little past the word (like "help the" in the reference).
  - Optional: `--pen-wash` background with `padding: var(--space-lg)` and no border-radius larger than `4px`. Try it without first.
  - Other columns stay at full opacity — don't dim them; they're still useful.

### 5.3 "Tired?" link

- Centered at the bottom, `--fs-body`, serif, `--ink`.
- Underlined with a thin hand-drawn stroke in `--ink` (not a CSS `text-decoration`).
- On hover/focus: the underline redraws in `--pen` green.
- Whatever it links to (a deload/light version, a rest-day note, a section that expands), it should be a real `<a>` or `<button>` with a visible focus state.

### 5.4 The green pen (signature element)

This is the one memorable thing on the page. Get it right.

**Look:** a single-stroke felt-tip line, ~2.5px wide at desktop sizes, round caps and joins, slightly wobbly, never geometrically perfect. The circle does **not** close cleanly — it overshoots and the tail crosses itself, exactly like the Sequoia "daring" circle. Underlines sag a little and end with a slight hook or uptick.

**Build:** inline SVG absolutely positioned over/under the text. The text sits in a `position: relative` wrapper; the SVG is `position: absolute; inset: …; pointer-events: none; overflow: visible;` and `aria-hidden="true"`.

```css
.pen {
  fill: none;
  stroke: var(--pen);
  stroke-width: 2.5;
  stroke-linecap: round;
  stroke-linejoin: round;
  vector-effect: non-scaling-stroke;
}
```

Use `preserveAspectRatio="none"` so the shape stretches to fit any word length.

**Starter paths** (tweak by hand until they feel loose):

```html
<!-- Loose circle, overshooting at the top-left — wrap around a word -->
<svg class="pen-mark pen-circle" viewBox="0 0 200 80" preserveAspectRatio="none" aria-hidden="true">
  <path class="pen" d="M38 14 C 90 2, 175 6, 192 34 C 204 58, 150 76, 92 74 C 40 72, 4 60, 8 38 C 12 18, 52 8, 110 10"/>
</svg>

<!-- Sagging underline with a small uptick at the end -->
<svg class="pen-mark pen-underline" viewBox="0 0 300 20" preserveAspectRatio="none" aria-hidden="true">
  <path class="pen" d="M2 8 C 60 14, 130 16, 200 12 C 240 10, 270 9, 296 6"/>
</svg>
```

- Circle box: extend ~12% beyond the word on the left/right and ~35% above/below.
- Underline: sits just below the baseline, extends ~5% past the word on the right.

**Rules:**

- Allowed marks: one circle (the day name), one underline (today's workout heading), and the "Tired?" hover underline. That's it.
- Never use the pen as a border, box, or background shape.
- Never put more than one circle on screen.

### 5.5 Theme toggle

- A small text button in the top-right corner, sans `--fs-micro`, `--ink-soft`: "Night" in day mode, "Day" in night mode. No sun/moon icons.
- Default theme follows `prefers-color-scheme`. A manual choice sets `data-theme="light"` or `data-theme="dark"` on `<html>` and is saved in `localStorage` (wrapped in try/catch).

```css
:root { /* day tokens */ color-scheme: light; }

@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) { /* night tokens */ color-scheme: dark; }
}

:root[data-theme="dark"] { /* night tokens */ color-scheme: dark; }

body { background: var(--paper); color: var(--ink); }
```

---

## 6. Night mode specifics (Blackstone feel)

Night mode is not just inverted colors. Apply these extra adjustments under the dark tokens:

- Background is **pure `#000000`**.
- Headline gets slightly lighter weight (`font-weight: 350`) and tighter tracking (`-0.025em`) — white serif on black looks heavier than ink on cream.
- `.exercise-detail` switches to Figtree **300**.
- Workout headings may switch to the sans (Figtree 300, `--fs-h2`) to echo Blackstone's "Unmatched scale" subheads. Decide once and apply to all four.
- Pen marks keep their shape, colored `--pen` (lifted green). Stroke width drops to `2px` — bright strokes on black read thicker.
- Optional Blackstone touch: one short horizontal hairline (`3rem` wide, `1px`, `--ink`) centered above the headline. Use it in night mode only.

---

## 7. Motion

- **On page load:** the green circle around the day name draws itself (`stroke-dasharray`/`stroke-dashoffset` animation, ~700ms, `cubic-bezier(.65,0,.35,1)`), then the underline on today's workout heading draws (~400ms, starting 150ms after the circle finishes). This is the only automatic animation.
- **Hover on "Tired?":** underline color crossfades ink → green (150ms).
- **Theme switch:** background and text colors transition over 200ms.
- No fade-in-on-scroll, no hover lifts, no parallax.
- `@media (prefers-reduced-motion: reduce)`: marks appear fully drawn instantly; no transitions.

```css
.pen-mark .pen {
  stroke-dasharray: 1;
  stroke-dashoffset: 1;
  animation: draw 700ms cubic-bezier(.65,0,.35,1) forwards;
}
/* add pathLength="1" on each <path> so dasharray 1 = full length */
@keyframes draw { to { stroke-dashoffset: 0; } }
```

---

## 8. Accessibility & quality floor

- Semantic structure: one `<h1>`, four `<section>`s each with an `<h2>`, lists as `<ol>`.
- All pen SVGs are `aria-hidden="true"`; today's workout is also announced in text (e.g. visually hidden "— today" inside its `<h2>`).
- Visible keyboard focus: `outline: 2px solid var(--pen); outline-offset: 4px;` on links and buttons.
- Tap targets ≥ 44×44px (theme toggle and "Tired?").
- Minimum body text 18px on mobile.
- Mobile safe areas: `<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">` and pad `:root` with `env(safe-area-inset-top/bottom, 0px)`.
- Works with JavaScript off: all four workouts still render; only the day name and today highlight are missing (headline falls back to "Today’s workouts").

---

## 9. Implementation notes

- One static `index.html` with inline `<style>` and `<script>`. No framework, no build step.
- Only external requests: Google Fonts.
- Routine data lives in one JS object at the top of the script so it's easy to edit:

```js
const routine = {
  "upper-a": { name: "Upper A", exercises: [{ name: "Bench press", detail: "3 × 8" }] },
  "lower-a": { name: "Lower A", exercises: [] },
  "upper-b": { name: "Upper B", exercises: [] },
  "lower-b": { name: "Lower B", exercises: [] },
};

// 0 = Sunday … 6 = Saturday. null = rest day. Fill in my actual split.
const schedule = { 0: null, 1: "upper-a", 2: "lower-a", 3: null, 4: "upper-b", 5: "lower-b", 6: null };
```

- Favicon: a single emoji (e.g. 🏋️) via an SVG data URI.

---

## 10. Do / Don't

| Do | Don't |
|---|---|
| Let one line of serif text float in lots of cream space | Fill space with cards, boxes or dividers |
| Use green only for pen marks and focus | Color headings, numbers or buttons green |
| Make pen marks loose and imperfect | Use perfect ellipses or CSS borders as "circles" |
| Keep everything sentence case | Use ALL CAPS labels or eyebrow text above headings |
| Use `×` and `–` in sets/reps | Write `3x8` or `8-10` |
| Pure black in night mode | Charcoal or blue-black "dark mode" grays |
| One animation on load | Fade-ins on every section |
