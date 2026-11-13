# Spec Delta

## Purpose

Gives a quick push on low-energy days: a "Tired?" button at the bottom of the page opens a small popup showing the next David Goggins quote in a fixed rotation.

## ADDED Requirements

### Requirement: Tired button
The page SHALL show a centered "Tired?" `<button>` at the bottom, in serif `--fs-body` `--ink`, underlined by a thin hand-drawn ink stroke that crossfades to `--pen` green on hover and keyboard focus (150ms), with a tap target of at least 44×44px.

#### Scenario: Hover
- **WHEN** the pointer hovers "Tired?"
- **THEN** its underline turns green

### Requirement: Quote popup opens as a modal dialog
Activating "Tired?" SHALL open a modal dialog showing one quote in serif `--fs-body` `--ink`, left-aligned, without added quotation marks, followed on its own line by `— David Goggins` in sans `--fs-small` `--ink-soft`, right-aligned.

#### Scenario: Open popup
- **WHEN** the user clicks "Tired?"
- **THEN** a modal popup appears showing a quote and the attribution `— David Goggins`, and the rest of the page is inert

### Requirement: Popup appearance
The popup SHALL be a box with 6px corner radius, `--paper` background, `1px solid var(--rule)` border, no shadow, width `min(30rem, 100% - 2rem)`, `--space-xl` padding, and max height 80vh with internal scrolling. The backdrop SHALL be `--paper` at about 70% opacity.

#### Scenario: Long quote on mobile
- **WHEN** the longest quote is shown at 375×667
- **THEN** the popup fits within the viewport and its content scrolls inside the box

### Requirement: Quote rotation
The page SHALL use the exact 25-quote list from `PROMPT.md`, pick a random starting index on load, show the next quote in order on each open, and wrap to the first after the last.

#### Scenario: Consecutive opens
- **WHEN** the user opens the popup, closes it, and opens it again
- **THEN** the second open shows the quote immediately following the first in the list

#### Scenario: Wrap around
- **WHEN** the last quote in the list was shown and the popup is opened again
- **THEN** the first quote in the list is shown

### Requirement: Closing the popup
The popup SHALL close on Esc, on a click on the backdrop, or on a small sans `Close` text button (`--fs-micro`, `--ink-soft`) at the bottom-left of the box. On close, focus SHALL return to "Tired?".

#### Scenario: Backdrop click
- **WHEN** the user clicks outside the box on the backdrop
- **THEN** the popup closes and "Tired?" has focus

#### Scenario: Click inside the box
- **WHEN** the user clicks on the quote text
- **THEN** the popup stays open

#### Scenario: Esc key
- **WHEN** the user presses Esc while the popup is open
- **THEN** the popup closes and "Tired?" has focus

### Requirement: Popup motion
The popup SHALL fade in over 150ms, and appear without animation under `prefers-reduced-motion: reduce`.

#### Scenario: Reduced motion open
- **WHEN** reduced motion is requested and the user opens the popup
- **THEN** it appears instantly
