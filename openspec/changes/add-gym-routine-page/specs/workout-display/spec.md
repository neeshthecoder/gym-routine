# Spec Delta

## Purpose

Presents all four workouts of the upper/lower split as readable typographic columns that fit a laptop screen without scrolling and stack sensibly on phones, in the Sequoia/Blackstone visual style.

## ADDED Requirements

### Requirement: Static single-file delivery
The site SHALL consist of `index.html` (inline CSS and JS, no framework or build step) and an empty `.nojekyll` at the repo root, use only relative paths, and make no network requests other than Google Fonts.

#### Scenario: Served from a project subpath
- **WHEN** the page is served at `https://<user>.github.io/<repo>/`
- **THEN** it renders fully and the browser network log shows only Google Fonts requests besides the page itself, and the console shows no errors

### Requirement: Four workout columns
The page SHALL show Upper A, Lower A, Upper B and Lower B in that order, each as a `<section>` with an `<h2>` heading ending in a colon (e.g. `Upper A:`), then one micro line (`--fs-micro`, sans, `--ink-soft`) with the focus and rest note, then an `<ol>` exercise list without visible markers.

#### Scenario: Column content
- **WHEN** the Upper A column renders
- **THEN** it shows `Upper A:`, a line containing "Strength & thickness" and "Alternate A1/A2 and B1/B2. Rest 60–90 s between movements.", and seven exercises ending with "Incline treadmill walk"

### Requirement: Exercise items
Each exercise SHALL show its name in serif (`--fs-body`, `--ink`) on one line and its detail in sans (`--fs-small`, `--ink-soft`, tabular lining numerals) beneath, using the exact routine data including pairing labels (A1, A2, B1…), `×` and `–`. Cardio SHALL be the last item, styled the same.

#### Scenario: Exercise with pairing label
- **WHEN** the first Upper A exercise renders
- **THEN** it shows "Barbell bench press" with the detail "A1 · 2 × 5–7 · 1 RIR" beneath

#### Scenario: Long name wraps
- **WHEN** "Pull-ups or neutral-grip pulldown" is wider than its column
- **THEN** it wraps within the column without overflowing into the next

### Requirement: Desktop no-scroll layout
At viewport widths ≥ 900px the page SHALL show the headline centered at top, the four columns side by side in the middle, and "Tired?" centered at the bottom, with no vertical scroll at 1280×720, 1366×768, 1440×900 and 1920×1080 in both themes, and `--fs-body` no smaller than 1rem.

#### Scenario: Short laptop screen
- **WHEN** the page is viewed at 1280×720 on Upper B day in night mode
- **THEN** the document height does not exceed the viewport and all four columns and "Tired?" are visible

#### Scenario: Large screen
- **WHEN** the page is viewed at 1920×1080
- **THEN** content is centered within a max width of 72rem with no vertical scroll

### Requirement: Tablet layout
At widths 600–899px the page SHALL show the workouts in a 2×2 grid (Upper A / Lower A on top, Upper B / Lower B below) and MAY scroll vertically.

#### Scenario: Tablet grid
- **WHEN** the page is viewed at 768px wide
- **THEN** the workouts appear in two columns and two rows in schedule order

### Requirement: Mobile layout
Below 600px the page SHALL stack everything in one scrolling column with a left-aligned headline, body text at least 18px, today's workout first directly under the headline (others in normal order), and "Tired?" at the bottom.

#### Scenario: Mobile on a workout day
- **WHEN** the page is viewed at 375×667 with `?day=4`
- **THEN** Upper B is the first workout under the headline, followed by Upper A, Lower A, Lower B

#### Scenario: Mobile on a rest day
- **WHEN** the page is viewed at 375×667 with `?day=0`
- **THEN** the workouts appear in normal order Upper A, Lower A, Upper B, Lower B

### Requirement: Pen marks
Pen marks SHALL be loose, single-stroke, imperfect hand-drawn SVG strokes in `--pen`, hidden from assistive technology, limited to the weekday circle, today's heading underline, and the "Tired?" hover/focus underline. Non-today headings SHALL carry at most a thin ink-colored hand-drawn underline.

#### Scenario: Only one circle
- **WHEN** any day is shown
- **THEN** at most one green circle is visible on the page

### Requirement: Load animation
On first load the weekday circle SHALL draw itself (~700ms), then today's heading underline SHALL draw (~400ms, starting ~150ms later). No other automatic animation SHALL run, and under `prefers-reduced-motion: reduce` marks SHALL appear fully drawn with no transitions.

#### Scenario: Reduced motion
- **WHEN** the user's system requests reduced motion
- **THEN** the circle and underline appear fully drawn immediately and theme changes are instant

### Requirement: No-JavaScript fallback
With JavaScript disabled the page SHALL still render all four workout columns with full content and the headline SHALL read "Today’s workouts", with no column marked.

#### Scenario: JS disabled
- **WHEN** the page is loaded with JavaScript turned off
- **THEN** the headline reads "Today’s workouts" and all four columns with all exercises are visible
