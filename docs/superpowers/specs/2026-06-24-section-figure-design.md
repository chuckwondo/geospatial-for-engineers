# Design: the `Section` figure (Trajectory × VerticalProfile = a curtain)

**Date:** 2026-06-24
**Status:** Approved (brainstorm)
**Artifact:** `diagrams/section-curtain.svg`, embedded in
`formats/coveragejson.qmd` at the **end of §4.2** as `#fig-section`.
**Visual source of truth:** the approved animated mockup
`.superpowers/brainstorm/4422-1782301284/content/section-animated-list-v3.html`
(converged with Chuck via the visual companion: a static v1→v15 composition pass,
then an animation pass that replaced the static worked-instance callout with a
synced sweep + composite-axis list). The static `section-dialup-v15.html` records
the composition decisions that still hold for the equation + curtain.

## Purpose

Complete the domain-types tour by teaching `Section` — the one common
CoverageJSON domain type that combines **both** axis structures the guide has
already drawn. A `Section`'s domain has a **composite axis** whose entries are
`(t, x, y)` tuples (exactly like `Trajectory`, §4.2) **crossed with** a
multi-valued **`z` axis** (exactly like `VerticalProfile`). Range values
therefore form a 2-D field indexed by `[station-along-track, depth]`.

The figure makes that decomposition literal:

> **Section = Trajectory × VerticalProfile**

It is the follow-on the domain-types gallery caption defers to ("the full
vocabulary, including `Section`, is in [spec §9.10]"). Chuck explicitly wanted it
for completeness despite the type's rarity.

## Subject

An **oceanographic section (CTD transect)** — the canonical real-world `Section`,
and the source of the type's name. A research ship sails a track, stopping at
numbered **stations**; at each station it lowers a CTD to record a column of
values (here, temperature) down through the water. The classic visualization is a
**curtain plot**: stations across, depth down, a value at each cell.

"Station" is the real hydrographic term and is the figure's consistent word for
"one entry of the composite axis / one track step." (Earlier drafts drifted
between "track step" and "station"; the final figure uses **station**
throughout.)

## Core teaching device — the equation

Read left-to-right as `A × B = C`, with **both factors drawn as domain/axis
structure (indigo)** and the **product drawn as the field of range values
(teal)** — the series' established color language:

1. **Trajectory** (factor, indigo): a small map — a winding ship track with four
   numbered station dots (`1` bottom-left → `4` top-right). Header label above the
   map: *"stations along the ship track"* (on the same baseline as the curtain's
   `1 2 3 4` column numbers, tying the two). Structural label below:
   *"composite (t, x, y) axis"*.
2. **× glyph** (neutral slate).
3. **VerticalProfile** (factor, indigo): a single vertical column of four cells
   with `z` ticks `0 / 10 / 20 / 30`. Label below: *"z axis (depth, m)"*.
4. **= glyph** (neutral slate).
5. **Section** (product, teal): the curtain — a 4 (stations) × 4 (depths) grid of
   teal cells, each carrying a **value reading**; station numbers `1 2 3 4`
   left-to-right. Header *"Section"*; structural label below: *"value at each
   (station, depth) · °C"*.

### The animated sweep + composite-axis list (replaces the static callout)

The worked-instance teaching is delivered by **motion** instead of a pinned
callout (the two can't coexist — a moving highlight beside a fixed callout is
incoherent). Three views share **one amber highlight** that advances station by
station, left-to-right, on an 8-second loop:

- **Track** — an amber ring around the active station's map dot.
- **Composite-axis list** — a labelled list *below* the equation showing the axis's
  real `(t, x, y)` tuples, one row per station, with column headers `# / t / x / y`
  (headers are essential — without them `x`/`y` read as temperatures). A square
  amber outline marks the active row.
- **Curtain** — a square amber outline around the active station's depth column.

The highlight walks **rightward on the track, downward through the list, rightward
across the curtain** — all in sync. The list's `t` **advances** (`12:00:00Z` →
`13:00:00Z` → `14:00:00Z` → `15:00:00Z`) and `x, y` drift along the track, so the
reader sees that *each composite-axis entry is a `(t, x, y)` tuple* and that the
axis carries the ship **through time** — the `…Series`/time idea Chuck most wanted
this figure to land. The depth values for the active station are read directly from
its highlighted curtain column (the `VerticalProfile` factor's `z` ticks share the
curtain's row baselines, so depth ↔ value reads across).

**Motion mechanics (C layer, per `diagrams/README.md`):**

- CSS `@keyframes` in a `<style>` *inside* the SVG; no JS, no SMIL. Four keyframes
  (`a1`–`a4`), each a 25 % slot, **crossfading** at the boundaries (one fades out as
  the next fades in over the same window) so there are **no blank frames / no
  flicker** — the bug that killed the first naïve version. All highlight elements
  for a given station (ring + list row + column) share that station's keyframe, so
  the three views stay locked together.
- **Reduced-motion guard:** `@media (prefers-reduced-motion: reduce)` stops the
  animation and freezes on **station 1** (ring on dot 1, list row 1, column 1) — a
  complete static worked instance, satisfying information parity.
- Embeds via Markdown `![](…)` (`<img>`); CSS-in-SVG animation runs there (verified
  pattern, cf. `row-major-grid.svg`).

## Values (invented but plausible)

Temperature (°C) decreasing with depth and gently along-track; floats throughout
so a **reading never looks like a position** (stations `1–4`, depths
`0/10/20/30`):

|z \ station|1|2|3|4|
|---|---|---|---|---|
|0 m|18.2|17.6|16.9|15.4|
|10 m|14.1|13.3|12.0|11.2|
|20 m|9.4|8.7|8.1|7.3|
|30 m|6.2|5.5|5.0|4.1|

Composite-axis tuples (one per station, `t` advancing one hour each, `x, y`
drifting along the track); station 1's `x = 12.5, y = 45.3` deliberately **reuses
the §5.2 PointSeries coordinates** so the worked instances across figures rhyme:

|#|t|x|y|
|---|---|---|---|
|1|2024-03-01T12:00:00Z|12.5|45.3|
|2|2024-03-01T13:00:00Z|12.8|45.6|
|3|2024-03-01T14:00:00Z|13.1|45.9|
|4|2024-03-01T15:00:00Z|13.4|46.2|

All `t` values are full RFC 3339 (seconds + `Z`).

## Layout & sizing (from the animated mockup)

- `viewBox="0 0 512 288"`, even ~16–20px margins on all four sides.
- **Top band — the equation** (≈ `y = 16…172`): three panels vertically centered on
  a shared mid-line, with `×` and `=` glyphs between them.
- Curtain: 46×26px cells, four columns (`x = 308/354/400/446`), four rows
  (`y = 52/78/104/130`); station numbers on `y = 46`.
- **Curtain cells stay an even grid.** Real CTD stations are *not* evenly spaced,
  but a tidy grid reads far better and matches the guide's `row-major-grid` cell
  vocabulary; the real-world irregularity is noted in the Quarto caption, not drawn.
- **Bottom band — the composite-axis list** (≈ `y = 190…275`): an intro label, a
  header row (`# / t / x / y`), and four monospace tuple rows; the active-row
  highlight is **square-cornered** (matching the curtain column outline) and
  padded evenly left/right off the measured text box (not estimated mono widths).

## Palette & accessibility (per `diagrams/README.md`)

- **Domain factors indigo, range curtain teal.** Trajectory/VerticalProfile use
  indigo (`#eef2ff`/`#e0e7ff` fills, `#4338ca` strokes, `#3730a3` headers); the
  curtain uses a **light teal sequential ramp** top→bottom
  (`#f0fdfa → #ccfbf1 → #99f6e4 → #5eead4`).
- **Readings stay dark-on-light everywhere.** The ramp was deliberately kept
  *light* so a single dark text fill (`#0f172a`, weight 600) is high-contrast in
  every cell (no white-on-mid-teal, which failed at ~2.5:1). The deeper = darker
  gradient is decoration only; value is carried by the **text label**, never color
  alone.
- **Crisp text — no inherited stroke.** Cell `<rect>`s carry the `#94a3b8` border
  in their own group; value readings are a separate **fill-only** group. (A shared
  group stroke was outlining the glyphs and reading as blur — fixed.)
- Amber `#f59e0b` reserved for the sweep highlight (map ring, list-row outline,
  curtain column outline) — highlight only, never a category color.
- **Refer to elements by role, never by color** in the `<desc>` and any in-figure
  key (e.g., "the highlighted column", "the active list row").
- White self-card; `role="img"` + linked `<title>`/`<desc>` (`aria-labelledby`),
  `<desc>` in the guide's vocabulary. The `<desc>` describes the figure including
  what the sweep does, so a screen-reader / reduced-motion reader gets the full
  concept without the motion.
- **Animated (C layer) — by exception.** The motion conventions default to static,
  but here the sweep teaches something a still cannot: that the composite axis
  carries the geometry *through time* (`t` advancing). The static reduced-motion
  fallback (frozen on station 1) carries the full concept, so information parity
  holds.

## Placement & caption

- Embed at the **end of §4.2** ("How axes encode their coordinates"), after the
  polygon composite-axis material (`#fig-polygon`) and before §4.3. Quarto id
  `#fig-section`. **Rationale (resolved with Chuck):** the figure's left factor *is*
  a composite axis, which §4.2 — not §4.1 — explains (`#fig-composite-axis`,
  `#fig-polygon`). Placing it here makes the figure a capstone that unites §4.2's
  composite axis with §4.1's `VerticalProfile`, instead of forward-referencing an
  unexplained concept. The §4.1 gallery already defers `Section` to "its own
  figure"; that pointer now lands in the very next subsection. A one-line lead-in
  in §4.2 prose should introduce it ("the one common type that uses *both* an
  independent `z` axis and a composite axis at once").
- Prose lives in the Quarto caption, not the SVG. The caption should: name a
  `Section` as a *trajectory of vertical profiles* (a composite `(t, x, y)` axis
  crossed with a `z` axis); gloss **station** = a stop where the ship lowers a CTD
  to record a depth profile; describe the sweep (one highlight ties each station's
  track dot, composite-axis entry, and curtain column, advancing through time as
  `t` ticks forward); and note that **real station spacing is irregular** even
  though the curtain is drawn as an even grid. Cite OGC 21-069r2 §9.10; the
  composite axis defers to §4.2 / GeoJSON RFC 7946 §3.1.6 as elsewhere.

## Verification

- `xmllint --noout diagrams/section-curtain.svg` clean.
- Renders on both `cosmo` (light) and `darkly` (dark) in the live `quarto preview`.
- **Animation runs when embedded via `<img>`** (verify in the live preview, not just
  as a standalone file); the three views stay in lockstep; crossfades show no blank
  frame / flicker.
- **Reduced-motion:** open the `.svg` as a top-level document and emulate
  `prefers-reduced-motion: reduce` (the override doesn't propagate into `<img>`);
  confirm it freezes on station 1 with all three highlights shown.
- Grayscale / Deuteranopia / Achromatopsia spot-check (DevTools → Rendering): every
  reading legible; domain/range distinction survives via shape + label, not hue.
- Readings and list rows render crisp (no glyph outline).
- `diagrams/README.md` inventory updated to list the new figure.

## Decisions log (from the brainstorm)

- **Layout A (curtain + inset map)** chosen over an isometric "profiles on a path"
  and a "map + separate profile bars" arrangement.
- **Dial up** to the full `Trajectory × VerticalProfile = Section` equation (over
  axes-only or a worked-tuple-only treatment).
- **Animate** (over static) — the sweep enforces the time dimension, which Chuck
  found the most compelling thing the figure could teach. This **reverses** the
  earlier static decision.
- **Composite-axis list + synced three-view sweep** replaces the static
  worked-instance callout (they can't coexist). An earlier per-station
  cross-fading text readout was rejected for flicker; the static list with a moving
  highlight fixed it.
- **Sweep left-to-right, `t` advancing forward** (station 1 on the left), matching
  the guide's time-flows-rightward convention (`multipointseries-through-time.svg`).
- **Station** terminology unified and established on the Trajectory.
- **Even curtain grid**, with the irregular-spacing caveat in the caption.
- **Full ISO 8601 timestamp** with seconds.
- Reuse §5.2 coordinates for the worked instance.
- **Filename `section-curtain.svg`** (rejected `section-trajectory-x-profile.svg`:
  the `x` reads too easily as the `x` coordinate).
- **Placed at the end of §4.2**, not §4.1 — so the composite axis is already
  explained when the figure leans on it (Chuck's call).

## Out of scope

- A Regular-vs-irregular axis diagram was **decided against** earlier (the
  start/stop/num vs explicit-values JSON already shows that compression).
