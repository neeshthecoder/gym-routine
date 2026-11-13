# Build: gym routine page (GitHub Pages)

Build a single static page that tells me what to train today and lists all four of my workouts. It's hosted on GitHub Pages from the root of this repo.

**Before writing any code, read both reference files in this repo:**

- `design.png` — my hand-drawn sketch of the layout. Left is desktop (headline, four columns side by side, "Tired?" at the bottom, no scrolling); right is mobile (everything stacked in one scrolling column, "Tired?" at the bottom). Treat it as the layout source of truth for arrangement and proportions, not for exact styling.
- `styleguide.md` — color tokens, type, spacing, the green pen marks, motion and accessibility. Its layout section describes the same sketch in more detail.

Where this prompt, the sketch and the style guide disagree, **this prompt wins**, then the sketch for layout, then the style guide. The overrides to the style guide are listed in section 3.

---

## 1. Deliverables

- `index.html` at the repo root. One file: inline `<style>` and `<script>`, no framework, no build step, no npm.
- An empty `.nojekyll` file at the repo root.
- The only external requests are Google Fonts (link given in the style guide).
- All paths relative so it works at `https://<user>.github.io/<repo>/`.

## 2. What the page does

1. Reads the device's local date and shows **"Today is ‹Weekday›"**, with the weekday circled by the green pen.
2. Directly under the headline, one small line (`--fs-small`, sans, `--ink-soft`) says what kind of day it is:
   - Workout day → the workout name and focus, e.g. `Upper A · strength & thickness`
   - Wednesday → `Rest day · light walk if you feel like it`
   - Saturday / Sunday → `Rest day`
3. Shows all four workouts as columns (Upper A, Lower A, Upper B, Lower B). Today's column gets the green underline under its heading. On rest days, no column is marked.
4. "Tired?" at the bottom opens a small quote popup (section 6).
5. Theme switches automatically by time of day (section 5). Clicking the "o" in "Today" flips it manually for testing.

### Schedule (JS `getDay()`: 0 = Sunday … 6 = Saturday)

```js
const schedule = { 0: null, 1: "upper-a", 2: "lower-a", 3: null, 4: "upper-b", 5: "lower-b", 6: null };
```

## 3. Overrides to the style guide

| Style guide says | Do this instead |
|---|---|
| Theme follows `prefers-color-scheme`, saved in `localStorage` | Theme follows **local time of day** (section 5). Do not read `prefers-color-scheme`. Do not use `localStorage` at all. |
| "Night"/"Day" text toggle button in the top-right corner | **No toggle button.** The hidden toggle is the "o" in "Today" (section 5). |
| Only rest days get a line under the headline | Every day gets the status line from section 2.2. |
| Exercise lists have no numbers | Keep `list-style: none`, but show the pairing label (A1, A2, B1…) inside the detail line — supersets matter at the gym. |
| "Tired?" links somewhere | "Tired?" is a `<button>` that opens a quote popup. |
| No cards / boxes | The quote popup is the one allowed exception (section 6). |

## 4. Layout requirement: no scrolling on desktop

**At ≥ 900px wide, the whole page must fit in the viewport with no vertical scroll** at common laptop sizes. Verify at 1280×720, 1366×768, 1440×900 and 1920×1080.

The style guide's spacing (`--space-3xl` above the headline, `--space-2xl` gaps) will overflow with this much content. On desktop:

- Make the page a full-height flex column (`height: 100%` on `html, body`, not `100vh`), headline at top, columns in the middle, "Tired?" at the bottom.
- Replace the big vertical spacers with values that scale on height, e.g. `clamp(1rem, 6vh, 6rem)`, so short screens compress the empty space first.
- Tighten exercise item gap if needed (`--space-sm` to `--space-md`) before shrinking any type.
- Keep `--fs-body` ≥ 1rem on desktop. If it still doesn't fit at 1280×720, it's okay to reduce `--fs-display`.
- Long names (e.g. "Pull-ups or neutral-grip pulldown") wrap within their column; that's fine.

Tablet (600–899px) and mobile (< 600px) follow the style guide and may scroll. On mobile, today's workout moves to the top of the stack.

## 5. Theme: automatic by time, with a hidden manual flip

- **Night mode** (Blackstone tokens) from **19:00 to 06:59** local time.
- **Day mode** (Sequoia tokens) from **07:00 to 18:59** local time.
- Set it with `data-theme="dark"` / `data-theme="light"` on `<html>`. Put a tiny inline script in `<head>` that sets the attribute before first paint so there's no flash of the wrong theme.
- Re-check every 60 seconds and on `visibilitychange`, so a tab left open flips at 7 p.m. / 7 a.m. Also re-check the weekday then (a tab left open past midnight should update the headline and today's column, without replaying the draw-on animation).

**Hidden manual flip:** the "o" in "Today" is wrapped in a `<button class="theme-o">`:

- Styled to look exactly like the surrounding letter (inherit font, size, color, letter-spacing; no background, border, padding or underline). The word must not visually shift or gain a gap.
- Clicking it toggles between day and night.
- The manual choice is **not saved**. It lasts until the page is reloaded or until the next 7 a.m. / 7 p.m. boundary, whichever comes first; then automatic takes over again.
- Accessibility: give the button `aria-label="Switch color theme"`. Make sure the `<h1>` still reads as "Today is Monday" to screen readers (e.g. put the visible heading text in an `aria-hidden` span and add a visually hidden full-text copy, or equivalent). Keep the green focus ring on keyboard focus (`:focus-visible` only).
- Expand its tap area to ~44×44px with a pseudo-element, without affecting layout.

**Testing aid:** support a `?day=0`–`?day=6` query parameter that overrides the weekday, so I can preview rest days and each workout day. Ignore invalid values.

## 6. "Tired?" quote popup

- "Tired?" is a centered `<button>` at the bottom, styled per style guide 5.3 (serif, ink, hand-drawn ink underline that turns green on hover/focus).
- Clicking it opens a native `<dialog>` with `showModal()`.
- **Popup look:** a square-ish box with slightly rounded corners (`border-radius: 6px`), background `--paper`, `1px solid var(--rule)` border, no shadow. Width `min(30rem, 100% - 2rem)`, generous padding (`--space-xl`). Backdrop: `--paper` at ~70% opacity (use `color-mix`, with a plain fallback).
- **Quote text:** same serif as the page, `--fs-body`, `--ink`, left-aligned. No quotation marks added around it.
- **Attribution:** on its own line below, sans, `--fs-small`, `--ink-soft`, right-aligned: `— David Goggins` (em dash + space).
- Long quotes: the box has `max-height: 80vh` and scrolls internally.
- **Close:** Esc, click on the backdrop, or a small sans `Close` text button (`--fs-micro`, `--ink-soft`) at the bottom-left of the box. Return focus to "Tired?" on close.
- **Cycling:** quotes go in order. On page load pick a random starting index; each time the popup opens, show the next quote; wrap around after the last one.
- Motion: popup fades in over 150ms. Nothing under `prefers-reduced-motion: reduce`.

### Quotes (use exactly this array)

I've already converted apostrophes and quote marks to typographic characters; keep the wording as-is, profanity included.

```js
const quotes = [
  "Whenever something is bothering me or stressing me, I fix it. I don’t wait for the next day or leave it like that.",
  "There’s purity in physical pursuits, because it doesn’t matter what your social status is, how people perceive you, it’s how long you can stay in the “pool”, how far can you run. In the real moments, what matters is how true you are to your words.",
  "Life is one big psychological warfare you play on yourself. It’s what you say to yourself every single day. You are at war with your mind every single day. Your mind is a perfect ground for training yourself.",
  "Embrace suffering, you have to suffer.",
  "Examine your brain, go back to the origin of your suffering, where your life started fucking up.",
  "I get over everything, water under the bridge. The grind does that. If you’re worried about someone who did something to you years ago, you are not grinding. If I grind hard, I don’t have time to worry about your monkey ass. I don’t have time to put you in the hate bank. There is great joy in grinding, great joy in suffering, it cleanses your soul and makes you grow up.",
  "I decided to face every fucking fear I have. People don’t realise how these fears haunt you at night.",
  "I couldn’t imagine being at 50, looking back and realise I haven’t tried anything.",
  "Anything that was horrible, I went towards it. I had to callous my mind. And then, I started opening different doors, I realised my potential is endless.",
  "I always thought, ‘Who would have the balls to do this? No one would. Except you. You are the baddest mofo.’ I used it as fuel. Eventually, it became a reality.",
  "I realised one demotivated day is part of the progress. Better than telling yourself, you’re not enough.",
  "If something haunts me, and bothers me, I would do it.",
  "You put your self help programs in a lazy situation, reading books on a couch, on a study table. What happens when you are in a tough situation? All that self talk goes away. But if you live through this shit…",
  "I know what’s on the back end of quitting, it’s a lifetime of regret and asking why you quit.",
  "We all want to learn to read about how to get somewhere, the only way is to live through hell to get what you want. You don’t learn from books, the effect is only temporary.",
  "The worst thing to believe is, at 40 years old you can’t do some things. You start to believe this nonsense. The worst thing is to be civilized. “At 40 years old, I’m good.” You’re never good. You’ve never arrived.",
  "Triple down on your weaknesses, not on your strengths. Find out something new about yourself.",
  "A happy guy is never someone who hasn’t faced something that bothered him daily.",
  "There is a quote: in a war of 100 people, 90 people shouldn’t be there, 9 people do the fighting, and 1 person is the warrior. I’m working towards being the one. That’s how I live my life.",
  "I put my phone and my shit away and I go dark. I go dark a lot.",
  "Self discipline, that’s all it is.",
  "I believe in patience, I am a patient dude. I can watch a grass grow for 20 years because that monk like mentality is what gets you through in life.",
  "Through repetition of things you don’t want to do, you build mental armor.",
  "I don’t judge people anymore. Because every person who did something to you has a bad past. I study them.",
  "In the past, I always faced my problems on a surface level, but when something bad happens, I quit. “Man, I thought I fixed this.” So I went deep into the dungeon of my soul to find out what makes me quit. So I put my sword in the fire repeatedly. I said, “We can’t quit, we have to figure out why you quit.” And eventually after too much beating, I noticed my brain is getting hard. You can’t dissect your brain in a normal situation. Dissect your brain when it’s miserable."
];
```

## 7. Routine data (use exactly this object)

Render each column as: heading (`Upper A:`), then the focus and rest note as one small line (`--fs-micro`, sans, `--ink-soft`) under the heading, then the exercise list. Each exercise: name in serif on one line, detail in sans underneath (tabular numerals). Cardio is the last item in each list, same styling.

Names are sentence case per the style guide. Details use `×` and `–`.

```js
const routine = {
  "upper-a": {
    name: "Upper A",
    focus: "Strength & thickness",
    note: "Alternate A1/A2 and B1/B2. Rest 60–90 s between movements.",
    exercises: [
      { name: "Barbell bench press",            detail: "A1 · 2 × 5–7 · 1 RIR" },
      { name: "Chest-supported row",            detail: "A2 · 2 × 6–8 · 1 RIR" },
      { name: "Overhead dumbbell press",        detail: "B1 · 2 × 8–10 · 0–1 RIR" },
      { name: "Lat pulldown",                   detail: "B2 · 2 × 8–10 · 0–1 RIR" },
      { name: "Rope tricep pushdown",           detail: "C1 · 2 × 10–12 · 0 RIR" },
      { name: "Dumbbell or cable bicep curl",   detail: "C2 · 2 × 10–12 · 0 RIR" },
      { name: "Incline treadmill walk",         detail: "Cardio · 10–15 min" }
    ]
  },
  "lower-a": {
    name: "Lower A",
    focus: "Quad & hinge focus",
    note: "Rest 90–120 s between heavy sets.",
    exercises: [
      { name: "Barbell back squat or hack squat", detail: "A1 · 2 × 5–7 · 1 RIR" },
      { name: "Hanging leg raises",               detail: "A2 · 2 × 10–15 · 0–1 RIR" },
      { name: "Romanian deadlift",                detail: "B1 · 2 × 6–8 · 1 RIR" },
      { name: "Standing calf raises",             detail: "B2 · 2 × 10–12 · 0 RIR" },
      { name: "Leg extension",                    detail: "C1 · 2 × 10–12 · 0 RIR" },
      { name: "Stationary bike",                  detail: "Cardio · 10–15 min" }
    ]
  },
  "upper-b": {
    name: "Upper B",
    focus: "Hypertrophy & width",
    note: "Alternate A1/A2 and B1/B2. Rest 60–90 s between movements.",
    exercises: [
      { name: "Incline dumbbell press",             detail: "A1 · 2 × 8–10 · 0–1 RIR" },
      { name: "Pull-ups or neutral-grip pulldown",  detail: "A2 · 2 × 8–10 · 0–1 RIR" },
      { name: "Machine shoulder press",             detail: "B1 · 2 × 8–10 · 0–1 RIR" },
      { name: "Seated cable row",                   detail: "B2 · 2 × 8–10 · 0–1 RIR" },
      { name: "Cable or dumbbell lateral raises",   detail: "C1 · 2 × 12–15 · 0 RIR" },
      { name: "Face pulls or reverse pec deck",     detail: "C2 · 2 × 12–15 · 0 RIR" },
      { name: "Elliptical or rower",                detail: "Cardio · 10–15 min" }
    ]
  },
  "lower-b": {
    name: "Lower B",
    focus: "Posterior & unilateral focus",
    note: "Rest 90–120 s between heavy sets.",
    exercises: [
      { name: "Trap bar deadlift or hip thrust",       detail: "A1 · 2 × 6–8 · 1 RIR" },
      { name: "Cable crunch or ab wheel",              detail: "A2 · 2 × 10–15 · 0–1 RIR" },
      { name: "Leg press or Bulgarian split squat",    detail: "B1 · 2 × 8–10 · 1 RIR" },
      { name: "Seated leg curl",                       detail: "B2 · 2 × 10–12 · 0 RIR" },
      { name: "Seated calf raises",                    detail: "C1 · 2 × 12–15 · 0 RIR" },
      { name: "Incline treadmill walk",                detail: "Cardio · 10–15 min" }
    ]
  }
};
```

The status line from section 2 uses `name` + `focus` (lowercased focus), e.g. `Upper A · strength & thickness`.

**No-JS fallback:** the four columns must still render with JavaScript off. Either write the routine into the HTML directly and have JS only add the day name / today marking, or render from the object and include a `<noscript>` copy. Prefer the first: hardcode the markup, and keep the `routine` object in sync as the single place I edit later (add a comment saying so). Headline falls back to "Today’s workouts".

## 8. Done when

- [ ] Desktop and mobile layouts match the arrangement in `design.png`.
- [ ] No vertical scroll at 1280×720, 1366×768, 1440×900, 1920×1080 in both themes.
- [ ] `?day=1`…`?day=5` and `?day=0`, `?day=3`, `?day=6` each show the right headline, status line and column marking (or none).
- [ ] Theme matches the clock on load; clicking the "o" flips it; reload returns to automatic.
- [ ] The "o" button is visually indistinguishable from the rest of "Today" in both themes.
- [ ] "Tired?" opens the popup, each open shows the next quote, Esc/backdrop/Close all close it and focus returns.
- [ ] Mobile (375×667): single column, today's workout first, body text ≥ 18px, popup fits and scrolls internally if needed.
- [ ] Pen draw-on animation runs once on load; nothing animates under reduced motion.
- [ ] No console errors. No requests other than Google Fonts.

When finished, tell me how to enable GitHub Pages for this repo (Settings → Pages → deploy from `main`, root).
