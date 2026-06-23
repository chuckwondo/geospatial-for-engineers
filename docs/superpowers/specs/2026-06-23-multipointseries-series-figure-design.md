# Design: the `…Series` flipbook figure (MultiPointSeries through time)

**Date:** 2026-06-23
**Status:** Approved (brainstorm)
**Artifact:** `diagrams/multipointseries-through-time.svg`, embedded in
`formats/coveragejson.qmd` §4.1 as `#fig-series-through-time`.

## Purpose

Teach the CoverageJSON `…Series` concept with more visual impact than the
existing single-point figures. A `…Series` domain type adds a multi-valued `t`
axis to **carry the same geometry through time** (§4.1 prose, OGC 21-069r2
[§9.10](https://docs.ogc.org/cs/21-069r2/21-069r2.html#_2ebca2a8-cdde-41b4-ad83-2ede16d3792e)):
the *domain geometry stays fixed*, while the *range values change per timestep*.
The figure makes that domain-vs-range, fixed-vs-time-varying split literally
visible.

It is the follow-on the domain-types gallery's caption points to ("each shape
also has a `…Series` form that adds a `t` axis"). Subject chosen: **MultiPointSeries**
(a sensor network), because one point → many points is the most direct step up
from `PointSeries`, the worked-instance device fits it perfectly, and a spatial
scatter "lighting up" each frame is the highest-energy option while staying
visually distinct from the existing `pointseries-daily-curve.svg` line chart.

## Core teaching device

- **Each sensor = an indigo ring (position/domain) + a teal fill (value/range).**
  The ring is pixel-identical in every frame; only the fill changes. Ring stays,
  fill flows — that *is* "same geometry, values vary through time."
- **Amber worked instance:** one station is ringed in amber across all three
  frames, joined by a faint amber tie-line, with a small numeric value readout
  under it per frame (e.g., `12°` → `15°` → `13°`). This is the series' signature
  worked-instance pattern (cf. `coverage-as-function.svg`); it follows one
  station's reading over time and makes "the value at this position changes with
  `t`" concrete.

## Visual structure

- **Three frames, left to right**, each a light sub-card labelled `t0` / `t1` /
  `t2`, all sitting on a single `t →` axis arrow running beneath the row (the
  shared time axis the `…Series` adds).
- **Same ~6 positions in every frame**, in identical spots; one of them is the
  amber worked instance.
- **Teal colorbar legend** (low → high) mapping fill color to value.
- **Static** — the row already shows all timesteps; per the motion conventions in
  `diagrams/README.md`, animation would be redundant (no information a static
  frame can't carry), so no C layer.

## Palette & accessibility (per `diagrams/README.md`)

- Indigo `#4338ca` for position rings (domain). Teal **sequential ramp** for fills
  (range = teal), light → dark, e.g. `#ccfbf1` → `#5eead4` → `#0f766e`, chosen so
  steps differ in **luminance** (survives grayscale / achromatopsia). Amber
  `#f59e0b` for the worked instance only. Slate `#334155` for the `t →` axis and
  arrowhead; muted slate for frame labels.
- **No color-alone encoding:** the domain/range distinction is carried by *shape*
  (ring vs fill), not hue; the worked instance is carried by the amber outline +
  tie-line + numeric readout, not color alone; the value ramp is backed by the
  colorbar legend and the numeric readouts.
- White self-card (`#ffffff`, `#e2e8f0` hairline). `role="img"` plus linked
  `<title>` and `<desc>` (`aria-labelledby`), `<desc>` in the guide's vocabulary.
- Even margins; snug `viewBox`; in-SVG text limited to structural labels
  (`t0`/`t1`/`t2`, `t →`, colorbar ends, the worked-instance readouts).

## Placement & caption

- Embed in §4.1 immediately after the domain-types gallery (`#fig-domain-types`),
  illustrating the gallery caption's `…Series` sentence. Quarto id
  `#fig-series-through-time`.
- Caption (prose lives here, not in the SVG): same geometry carried through time;
  ring = position (domain, fixed), fill = value (range, time-varying); the amber
  station threads one sensor's changing reading across `t0`→`t1`→`t2`; note that a
  MultiPointSeries is, in effect, many `PointSeries` sharing one `t` axis.

## Verification

- `xmllint --noout diagrams/multipointseries-through-time.svg` is clean.
- Renders correctly in the live `quarto preview` on both `cosmo` (light) and
  `darkly` (dark) themes.
- Grayscale / Deuteranopia / Achromatopsia spot-check (DevTools → Rendering) per
  the conventions.
- README inventory updated to list the new figure.

## As-built deviations (2026-06-23)

Refined during review while building:

- **Value is a per-dot text label, not color alone.** The original plan encoded
  value by fill shade with a colorbar legend; that left value as a *color-alone*
  encoding for the non-highlighted dots (a convention violation that fails in
  grayscale). Every dot now carries a numeric value label; fill shade is kept only
  as redundant support, and the colorbar was dropped.
- **In-SVG heading + role-based key added.** A bold heading ("MultiPointSeries:
  one geometry, sampled through time") and a one-line key make the figure legible
  standalone. The key names elements by *role*, never by color ("same dots = fixed
  positions · only the value labels change with t"). This rule is now codified in
  `diagrams/README.md` ("Refer to elements by role, never by color").
- **Worked instance carries a position label, stacked `x=`/`y=`.** The highlighted
  station shows `x=12.5, y=45.3` (the §5.2 PointSeries coordinates) identically in
  all three frames, proving the position is fixed while the value changes. Stacked
  to avoid colliding with the flanking dots.
- **Tie is a pair of dashed arrows** (`t0`->`t1`, `t1`->`t2`) rather than one
  plain line, so time direction is explicit.

## Out of scope (tracked separately)

- The **`Section`** figure (a trajectory of vertical profiles) — Chuck wants it
  for completeness; its own brainstorm/spec.
- No animation (C layer) for this figure.
- No change to the §4.1 table or the gallery.
