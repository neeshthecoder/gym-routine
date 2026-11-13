# Proposal

## Why

I want a single glanceable page — on my laptop or my phone between sets — that tells me what to train today and shows all four workouts of my upper/lower split. Nothing exists yet; this change builds the page from scratch as a static site on GitHub Pages, following `PROMPT.md`, the `design.png` sketch, and `styleguide.md` (in that order of precedence).

## What Changes

- Add `index.html` at the repo root: one self-contained file with inline `<style>` and `<script>`, no framework, no build step, relative paths only. Google Fonts (Source Serif 4, Figtree) is the only external request.
- Add an empty `.nojekyll` at the repo root so GitHub Pages serves the file as-is.
- Headline "Today is ‹Weekday›" with a hand-drawn green pen circle around the weekday, plus a status line describing the day (workout name + focus, Wednesday light-walk rest, or weekend rest).
- Four workout columns (Upper A, Lower A, Upper B, Lower B) hardcoded in HTML from the `routine` object; today's column gets a green hand-drawn underline. Pairing labels (A1, A2, B1…) shown in each detail line.
- Responsive layout: desktop (≥ 900px) fits in the viewport with no vertical scroll; tablet 2×2; mobile single column with today's workout first.
- Theme chosen automatically by local time (night 19:00–06:59, day 07:00–18:59), set before first paint, re-checked every 60 s and on tab visibility; a hidden button on the "o" of "Today" flips it until reload or the next boundary. No `localStorage`, no `prefers-color-scheme`.
- "Tired?" button opens a native `<dialog>` that cycles through a fixed list of David Goggins quotes, starting at a random index.
- `?day=0`–`?day=6` query parameter to preview any weekday.
- One-time pen draw-on animation on load; nothing animates under reduced motion.
- No-JS fallback: all four columns render; headline reads "Today’s workouts".

## Capabilities

### New Capabilities
- `daily-schedule`: Determining today's weekday (with `?day=` override and midnight rollover), the headline and status line, and which workout column is marked as today.
- `workout-display`: Rendering the four workouts from the routine data, the responsive desktop/tablet/mobile layout (including the no-scroll desktop constraint and today-first ordering on mobile), pen marks and load animation, and the no-JS fallback.
- `color-theme`: Time-of-day day/night theme selection, pre-paint application, periodic re-checks, and the hidden "o" manual flip.
- `motivation-quotes`: The "Tired?" button and the cycling quote dialog, including its look, closing behavior and focus return.

### Modified Capabilities
<!-- None: no existing specs in openspec/specs/. -->

## Impact

- New files: `index.html`, `.nojekyll` at the repo root. No other code exists in the repo.
- Hosting: GitHub Pages, deployed from `main`, root folder.
- Dependencies: Google Fonts CSS/font files only; no npm, no JS libraries.
- Future edits to the routine happen in the `routine` object and the hardcoded markup, kept in sync (marked with a comment).
