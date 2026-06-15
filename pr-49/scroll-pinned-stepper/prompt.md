# Prompt: scroll-pinned stepper (scrollytelling)

## Task
Build a **scroll-pinned stepper** — the scrollytelling pattern where a graphic
freezes in place (sticky), updates through a series of states as the reader
scrolls past text "steps", then releases. This is the "sticky graphic" pattern
used by newsroom graphics desks (Reuters, NYT, The Pudding). Libraries that do
it: `scrollama` (vanilla) and `svelte-scroller` (Svelte). The freeze itself is
plain CSS `position: sticky` — no JS pins it.

## Goal
A self-contained `index.html` (no build step, no dependencies) with:
- A tall scroll track containing a `position: sticky; top: 0; height: 100vh`
  graphic layer and a column of text steps.
- The graphic visibly changes as each step reaches mid-viewport.
- The pin releases cleanly after the last step.

## Rules (these are the design, not decoration)
1. **Steps are data, not code.** Declare one array of `{ caption, state }`. The
   `state` object must carry *every* per-step difference (position, color,
   scale, label, whatever the graphic varies). Adding a step must mean adding one
   array entry and editing nothing else. If a new step forces a logic change, the
   schema is wrong — fix the schema, don't branch.
2. **One pure render function.** `render(state)` sets the graphic's appearance
   from the state value and runs identically every time. No `if (active === 0)`
   / per-step branches anywhere. Variability lives in the value passed in, never
   in which code runs.
3. **One source of truth for "active step."** A single `active` index, written in
   exactly one place. Both the graphic and any step highlighting derive from it.
4. **Single enforcer for step detection.** Exactly one `IntersectionObserver`
   (or one scroll handler) decides the active step. Do not scatter scroll math
   across multiple listeners that could disagree.
5. **The same array drives both layers.** Generate the text steps and the graphic
   states from one array so the caption and its graphic state cannot drift apart.
6. **No defensive guards for states that can't occur.** Initialize `active` with a
   real first paint rather than guarding every read against "no step yet."

## Avoid (anti-patterns that crystallize this pattern)
- ❌ One CSS rule or DOM element per step (`.step-1 {…} .step-2 {…}`). That is
  N types for one behavior; use one element whose look is a function of data.
- ❌ A `switch`/`if`-ladder on the active index inside the render path.
- ❌ JavaScript that manually computes scroll offsets to "pin" the graphic —
  `position: sticky` already does this; reimplementing it is rough and brittle.
- ❌ Two queries/listeners that both try to set the active step.

## Acceptance criteria
- Scrolling freezes the graphic, advances it through each declared state, and
  releases it after the last step.
- Adding a 5th step is a one-line data change with zero edits to render or
  observer logic. (This is the test for whether the schema is the strongest true
  theorem about the data.)
- No per-step conditionals exist in the code.
