# Spec Delta

## Purpose

Tells the user what to train today: resolves the current weekday (or a preview override), shows it in the headline with a status line, and marks the matching workout column.

## ADDED Requirements

### Requirement: Weekly schedule mapping
The page SHALL map JavaScript weekdays (0 = Sunday … 6 = Saturday) to workouts as: Monday → Upper A, Tuesday → Lower A, Thursday → Upper B, Friday → Lower B; Sunday, Wednesday and Saturday are rest days.

#### Scenario: Workout day resolves to its workout
- **WHEN** the resolved weekday is Thursday (4)
- **THEN** today's workout is Upper B

#### Scenario: Rest day resolves to no workout
- **WHEN** the resolved weekday is Saturday (6)
- **THEN** no workout is selected for today

### Requirement: Weekday headline
The page SHALL display an `<h1>` reading "Today is ‹Weekday›, ‹Routine›" using the device's local date, where ‹Routine› is today's workout name or "rest day", with ‹Routine› wrapped in a loose green hand-drawn loop. Screen readers SHALL hear the heading as the plain sentence.

#### Scenario: Headline on a Friday
- **WHEN** the page loads and the local weekday is Friday
- **THEN** the headline reads "Today is Friday, Lower B" and only "Lower B" carries the green loop

#### Scenario: Headline on a rest day
- **WHEN** the resolved weekday is Sunday
- **THEN** the headline reads "Today is Sunday, rest day" with "rest day" looped

#### Scenario: Accessible heading text
- **WHEN** a screen reader reads the `<h1>` on a Monday
- **THEN** it announces "Today is Monday, Upper A" without splitting the word "Today" or announcing the theme button as part of the heading

### Requirement: Day status line
Directly under the headline, the page SHALL show one small line (`--fs-small`, sans, `--ink-soft`) with detail the headline lacks: on workout days the workout's focus; on Wednesday `Light walk if you feel like it`; on Saturday and Sunday no line.

#### Scenario: Workout day status
- **WHEN** the resolved weekday is Monday
- **THEN** the status line reads `Strength & thickness`

#### Scenario: Wednesday status
- **WHEN** the resolved weekday is Wednesday
- **THEN** the status line reads `Light walk if you feel like it`

#### Scenario: Weekend status
- **WHEN** the resolved weekday is Saturday or Sunday
- **THEN** no status line is shown

### Requirement: Today's column marking
On a workout day, the page SHALL mark exactly one workout column as today, giving its heading the green hand-drawn underline and a visually hidden "— today" text. On a rest day, no column SHALL be marked.

#### Scenario: Workout day marks its column
- **WHEN** the resolved weekday is Tuesday
- **THEN** only the Lower A column has the green underline and the hidden "today" text

#### Scenario: Rest day marks nothing
- **WHEN** the resolved weekday is Sunday
- **THEN** no column has a green underline

### Requirement: Weekday preview override
The page SHALL accept a `day` query parameter with an integer value 0–6 that overrides the local weekday for the headline, status line and column marking. Any other value SHALL be ignored and the local weekday used.

#### Scenario: Valid override
- **WHEN** the page is opened with `?day=3` on a Monday
- **THEN** the headline reads "Today is Wednesday, rest day", the status line is the Wednesday walk line, and no column is marked

#### Scenario: Invalid override ignored
- **WHEN** the page is opened with `?day=9` or `?day=abc`
- **THEN** the page uses the device's local weekday

### Requirement: Midnight rollover
While the page stays open, the page SHALL re-check the weekday every 60 seconds and when the tab becomes visible, updating the headline, status line and today's column if the day changed, without replaying the pen draw-on animation.

#### Scenario: Tab left open past midnight
- **WHEN** the page was loaded on a Monday and the clock passes midnight into Tuesday
- **THEN** within 60 seconds (or on returning to the tab) the headline reads "Today is Tuesday, Lower A", the status line shows its focus, Lower A is marked, and the marks appear already drawn

#### Scenario: Override persists across re-checks
- **WHEN** the page was opened with a valid `?day=` value
- **THEN** periodic re-checks keep showing the overridden weekday
