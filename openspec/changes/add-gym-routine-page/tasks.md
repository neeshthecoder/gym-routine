# Tasks

## 1. Scaffold and static content

- [x] 1.1 Create an empty `.nojekyll` at the repo root and verify it exists with size 0 (`ls -l .nojekyll`)
- [x] 1.2 Create `index.html` skeleton: doctype, `lang="en"`, viewport meta with `viewport-fit=cover`, title, emoji SVG data-URI favicon, Google Fonts preconnect + stylesheet link from the style guide, inline `<style>` and `<script>`; verify it opens from `file://` with no console errors
- [x] 1.3 Add the pre-paint `<head>` script that sets `data-theme` from local hour (dark if ≥ 19 or < 7) and adds `js` class to `<html>`; verify in DevTools that `<html>` has the right attributes before the body parses (set system clock / override `Date` in console and reload)
- [x] 1.4 Hardcode the four `<section>`s (Upper A, Lower A, Upper B, Lower B) from the PROMPT.md `routine` data: `<h2>` with colon + underline SVGs + hidden "— today" span, micro focus/note line, `<ol>` of name/detail spans; verify every name/detail matches PROMPT.md character-for-character (`×`, `–`, pairing labels)
- [x] 1.5 Add `routine`, `schedule` and `quotes` constants at the top of the script exactly as given, with a "keep in sync with the markup below" comment; verify by diffing the pasted arrays against PROMPT.md
- [x] 1.6 Add the headline markup: `.headline-fallback` ("Today’s workouts") and `.headline-js` (`aria-labelledby` h1, aria-hidden "T", `<button class="theme-o" aria-label="Switch color theme">o</button>`, aria-hidden "day is ", day span with circle SVG, `sr-only` full text), status line element, and footer "Tired?" button + `<dialog>`; verify with JS disabled that only "Today’s workouts" and four full columns appear

## 2. Styling: tokens, type and layout

- [x] 2.1 Define day tokens on `:root` and night tokens on `:root[data-theme="dark"]` (no `prefers-color-scheme`), type scale, spacing scale, fonts, `body { background: var(--paper); color: var(--ink) }`, 200ms color transition; verify toggling the attribute in DevTools swaps cream/black with correct ink colors
- [x] 2.2 Style headline, status line (`--fs-small`, sans, `--ink-soft`), workout headings, micro note line, exercise name/detail (tabular lining nums), and night-mode specifics (headline 350 weight/-0.025em, detail Figtree 300, pen stroke 2px, optional hairline); verify visually in both themes
- [x] 2.3 Style `.theme-o` (inherit everything, `display: inline`, no box, 44×44 `::after` tap area, `:focus-visible` green outline only); verify at 200% zoom in both themes that "Today" shows no gap/offset vs. plain text and that Tab shows the ring while mouse click does not
- [x] 2.4 Implement desktop (≥ 900px) full-height flex layout with height-based clamps per design §4 and 4-column grid (max 72rem, centered); verify `document.documentElement.scrollHeight <= innerHeight` at 1280×720, 1366×768, 1440×900, 1920×1080 in both themes with `?day=1` and `?day=4` (7-item columns); tune spacing/item gap/display size until all pass with `--fs-body` ≥ 1rem
- [x] 2.5 Implement tablet (600–899px) 2×2 grid and mobile (< 600px) single column with left-aligned headline, body ≥ 18px, `[data-today="true"] { order: -1 }`, safe-area padding; verify at 768×1024 and 375×667 that order and sizes are correct and there's no horizontal scroll
- [x] 2.6 Add pen mark styles (circle/underline positioning, ink underline for non-today headings, green for today), draw-on keyframes gated by `.animate` with circle 700ms then underline 400ms after ~150ms gap, and reduced-motion overrides; verify the animation plays once on load and that with reduced-motion emulation marks appear drawn instantly and nothing transitions

## 3. Behavior: day, theme, quotes

- [x] 3.1 Implement `resolveDay()` (`?day=` 0–6 via `/^[0-6]$/`, else `getDay()`) and `applyDay(d)` (headline weekday + sr-only text, status line per spec, `data-today` + hidden "— today" on the right column or none); verify each of `?day=0`…`?day=6` and `?day=9`/`?day=abc` shows the expected headline, status line and marking
- [x] 3.2 Implement theme controller: `manualTheme`, `lastAutoTheme`, click handler on `.theme-o`, boundary detection clearing the override; verify click flips theme, reload returns to automatic, and (by overriding `Date` in console then triggering the tick) crossing 19:00/07:00 clears a manual flip
- [x] 3.3 Add the 60-second `setInterval` and `visibilitychange` tick that re-runs theme and day checks, removing `.animate` before re-applying a changed day; verify by stubbing the date to 23:59 → 00:00 that the headline, status and column update with marks shown fully drawn (no replay)
- [x] 3.4 Implement the quote dialog: random start index, show-then-advance on each open, `showModal()`, backdrop-click close via `event.target === dialog`, Close button, focus return to "Tired?" on `close`; verify consecutive opens show consecutive quotes, wrap from last to first (set index in console), and Esc/backdrop/Close each close it with focus on "Tired?"
- [x] 3.5 Style the dialog and "Tired?" button per spec (6px radius, `--paper`, `--rule` border, no shadow, `min(30rem, 100% - 2rem)`, `--space-xl` padding, `max-height: 80vh` + overflow auto, `color-mix` backdrop with rgba fallback per theme, 150ms fade-in disabled under reduced motion, hand-drawn ink underline turning green on hover/focus, ≥ 44px tap target); verify at 375×667 that the longest quote scrolls inside the box, and in both themes the backdrop matches the paper color

## 4. Integration checks

- [ ] 4.1 Run the PROMPT.md §8 checklist end to end in Chrome and Safari (desktop + 375×667 emulation): layouts vs. `design.png`, no-scroll sizes in both themes, all `?day=` values, theme on load/flip/reload, popup behaviors, reduced motion, JS-off fallback; record any failures and fix them
- [x] 4.2 Verify the Network panel shows only `fonts.googleapis.com` / `fonts.gstatic.com` requests besides `index.html`, the Console has no errors or warnings, and no `localStorage` access occurs (search the file for `localStorage` and `prefers-color-scheme` → no matches)
- [x] 4.3 Serve the folder from a subpath (e.g. `python3 -m http.server` from the parent dir and open `/gym%20stuff/`) to confirm relative paths work as on `https://<user>.github.io/<repo>/`

## Workflow follow-up

- Tell the user how to enable GitHub Pages: Settings → Pages → Source "Deploy from a branch" → Branch `main`, folder `/ (root)` → Save.
- Archive the change once the user has reviewed the deployed page.
