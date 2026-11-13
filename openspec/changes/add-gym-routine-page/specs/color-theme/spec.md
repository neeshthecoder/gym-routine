# Spec Delta

## Purpose

Switches the page between the cream Sequoia day theme and the black Blackstone night theme based on local time, with a hidden, unsaved manual flip for testing.

## ADDED Requirements

### Requirement: Time-of-day theme
The page SHALL use night mode (Blackstone tokens) from 19:00 through 06:59 local time and day mode (Sequoia tokens) from 07:00 through 18:59, expressed as `data-theme="dark"` or `data-theme="light"` on `<html>`. It SHALL NOT read `prefers-color-scheme` or use `localStorage`.

#### Scenario: Evening load
- **WHEN** the page loads at 19:00 local time
- **THEN** `<html>` has `data-theme="dark"` and the background is `#000000`

#### Scenario: Morning load
- **WHEN** the page loads at 07:00 local time on a system set to dark mode
- **THEN** `<html>` has `data-theme="light"` and the background is `#F9F5EE`

### Requirement: No flash of wrong theme
The theme attribute SHALL be set before first paint so the page never briefly shows the other theme.

#### Scenario: Night load without flash
- **WHEN** the page loads at 22:00
- **THEN** the first painted frame already uses the black background

### Requirement: Automatic boundary re-check
The page SHALL re-check the time every 60 seconds and when the tab becomes visible, switching theme when the 07:00 or 19:00 boundary is crossed.

#### Scenario: Tab open across 19:00
- **WHEN** the page is open in day mode and the clock passes 19:00
- **THEN** within 60 seconds (or on returning to the tab) the page is in night mode

### Requirement: Hidden manual flip
The "o" in "Today" SHALL be a button (`aria-label="Switch color theme"`) that toggles between day and night when activated. The choice SHALL NOT be saved and SHALL last until reload or the next 07:00/19:00 boundary, whichever comes first.

#### Scenario: Flip then reload
- **WHEN** the user clicks the "o" at 14:00 and then reloads
- **THEN** the page switched to night mode on click and is back in day mode after reload

#### Scenario: Flip then boundary
- **WHEN** the user flips to night mode at 18:50 and the clock then passes 19:00
- **THEN** the manual override is cleared and automatic selection takes over (night mode), so a later flip at 19:05 switches to day mode until 07:00

#### Scenario: Flip persists before boundary
- **WHEN** the user flips to night mode at 14:00 and a 60-second re-check runs at 14:01
- **THEN** the page stays in night mode

### Requirement: Invisible toggle styling
The theme button SHALL be visually indistinguishable from the surrounding letters in both themes (same font, size, color, letter-spacing; no background, border, padding or underline; no shift or gap in the word), show the green focus ring only on keyboard focus, and have a tap area of about 44×44px that does not affect layout.

#### Scenario: Visual parity
- **WHEN** the headline renders in either theme
- **THEN** "Today" looks identical to plain text with no gap or offset around the "o"

#### Scenario: Keyboard focus
- **WHEN** the user tabs to the "o" button
- **THEN** a 2px green focus outline appears; clicking it with a mouse shows no outline

### Requirement: Theme transition
Background and text colors SHALL transition over about 200ms when the theme changes, and instantly under reduced motion.

#### Scenario: Animated switch
- **WHEN** the theme flips without reduced motion
- **THEN** colors crossfade over ~200ms
