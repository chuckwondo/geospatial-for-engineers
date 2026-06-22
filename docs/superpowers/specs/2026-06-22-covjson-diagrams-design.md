# Design: CoverageJSON guide diagram pass (establish the visual pattern)

- **Date:** 2026-06-22
- **Status:** Approved design; ready for implementation planning.
- **Author:** Chuck Daniels (with Claude)

## 1. Context and motivation

"Diagram-forward, engaging visuals" is a stated core goal of the *Geospatial for
Engineers* series, but the visual system has been exercised exactly once
(`diagrams/coverage-as-function.svg`). Before that system is committed to every
future guide, this pass hardens it on the one guide we understand deeply and have
designated the series template: the CoverageJSON guide
(`formats/coveragejson.qmd`).

The deliverable is a small, high-polish set of diagrams plus a codified
conventions document, so the *next* diagram (in any guide) is reproducible
against a proven standard. The prose pattern is already proven; this pass proves
the **visual** pattern.

## 2. Scope

In scope -- three diagrams and a conventions update:

1. **Coverage-as-spreadsheet** (guide Section 2.1) -- net-new hero. The guide's
   central mental image (a spreadsheet laid over a map; two stacked sheets for
   multiple ranges). It is a *companion* to the existing
   `coverage-as-function.svg`, not a replacement: the abstract domain -> range
   figure stays, and this concrete-intuition figure is added after the
   spreadsheet prose.
2. **Row-major grid fold** (Section 4.3) -- backlog diagram. Replaces the Section
   4.3 ASCII fold block. Gets the motion enhancement (Section 6).
3. **Parameter <-> range key binding** (Section 3.3) -- backlog diagram. Replaces
   the Section 3.3 ASCII block. Static.
4. **Update `diagrams/README.md`** to codify the matured conventions in Sections
   4-6 below.

Out of scope this round (done later, against this standard): the Section 5.1 SST
grid, Section 5.2 PointSeries, Section 5.5 land-cover grid, the Section 3.1
Coverage skeleton, and the text-based (Mermaid/Graphviz/D2) diagram lane.

## 3. Authoring approach

Diagrams are **hand-coded SVG**, committed directly as the source (matches the
existing `coverage-as-function.svg`; no Excalidraw/binary round-trip). Hand-coded
SVG is precise, diffs cleanly, and can carry inline CSS for motion and
accessibility metadata.

All three diagrams are hand-SVG this pass; the text-based lane (better suited to
object trees and taxonomies) is deferred to a future structural diagram that
actually fits it, rather than forcing one here to "exercise the lane."

## 4. Visual system -- the "B" bar (every diagram meets this)

- **Palette:** domain/inputs indigo `#6366f1`; range/outputs teal `#14b8a6`;
  neutral slate `#0f172a`; links slate-700 `#334155`; amber `#f59e0b` reserved
  exclusively for highlight/motion (never a category color).
- **Light card:** white (`#fff`) rounded rectangle with a hairline border
  (`#e2e8f0`), so every diagram is dark-on-light and floats on both the `cosmo`
  (light) and `darkly` (dark) site themes. One theme strategy; no dark-mode
  variants to maintain.
- **Level-2 quality floor:** soft depth (a subtle drop shadow is fine), clear
  typographic hierarchy, and at least one *explanatory* device (bracket, group,
  color-map, or annotation). A diagram must teach the rule, not merely label
  parts.
- **Vocabulary parity:** labels reuse the guide's prose terms verbatim (domain,
  range, axis, `shape`, `axisNames`, row-major, parameter), so reader sees the
  same words in prose and picture.

## 5. Color and accessibility rules

These are mandatory conventions, not aspirations:

- **Never encode meaning by color alone.** Every categorical distinction is also
  carried by position, a text label, or a shape/bracket. This is what makes the
  diagrams robust to all color-vision deficiencies, grayscale printing, and poor
  displays. (Domain vs range: left/right + labels; grid rows: brackets + labels.)
- **Maintain luminance separation between category colors.** Indigo (relative
  luminance ~0.19) is dark; teal (~0.37) is lighter -- distinguishable in
  grayscale and under red-green CVD. Verify each diagram with a grayscale check.
- **Contrast thresholds (WCAG 2.1):** text >= 4.5:1 (small) / 3:1 (large);
  meaningful strokes, borders, and connectors >= 3:1 against the card.
  - Consequence: light teal `#14b8a6` as a stroke on white is only ~2.5:1
    (fails). Use **pastel tints for fills** (`#eef2ff`, `#f0fdfa`, `#e0e7ff`,
    `#ccfbf1`) but **darker indigo/teal for strokes, borders, and small text**:
    `#4338ca` (indigo-700, ~6:1) and `#0f766e` (teal-700, ~5.5:1).
  - This also tightens the existing `coverage-as-function.svg` (its range card
    uses the light teal as a stroke); fixing it is part of this pass.
- **Amber is transient only.** Used solely for the motion highlight, never to
  encode a category. Reduced-motion users see the static frame, whose meaning
  never depends on amber.
- **No hatching/pattern fills.** Redundant encoding via position + labels +
  luminance is sufficient; patterns add clutter.
- **Accessibility metadata:** every SVG carries `role="img"` and linked
  `<title>` + `<desc>` describing the figure for screen readers.

## 6. Motion system -- the "C" enhancement (opt-in)

- Motion is **layered on top of a complete static B drawing.** The static
  rendering is always the fallback; build B first, then add motion.
- **Delivered as CSS `@keyframes` inside the `.svg` file** (no JavaScript), so it
  animates even when embedded as a Markdown `![](...)` image. Self-contained and
  diffable.
- **`@media (prefers-reduced-motion: reduce)` inside the SVG** freezes it to the
  static B frame. Also degrades gracefully for print and no-CSS contexts.
- **Applied only where motion teaches what a static frame cannot.** This pass:
  - Row-major grid fold: **yes** -- the fold *is* the lesson (a looping highlight
    walks each value into its cell in row-major order).
  - Coverage-as-spreadsheet hero: decide at mockup time; candidate is a subtle
    "value being read from a cell" touch.
  - Parameter <-> range binding: **no** (a gentle connector pulse at most).
- **Information parity:** anything the animation conveys (e.g., reading order)
  must also be recoverable from the static frame and the prose, so reduced-motion
  users lose nothing.

## 7. Per-diagram briefs

**7.1 `coverage-as-spreadsheet.svg` (Section 2.1, B; maybe subtle C)**
A translucent grid of cells overlaid on a simple map shape: row/column labels are
positions (the domain), cell values are the range. A second panel shows two
stacked sheets sharing one set of row/column labels -> the "several functions
over one domain" (multiple ranges) idea. Vocabulary: domain, range, positions,
values. The composition is the most uncertain, so it gets a visual-companion
mockup pass during implementation before the SVG is committed.

**7.2 `row-major-grid.svg` (Section 4.3, B + C)**
The flat `values` list `[1..6]`, bracketed into its two rows and color-mapped to
the 2x3 grid it lands in, with an "x varies fastest" cue. C adds a looping amber
highlight that walks each value into its cell in row-major order. (This is the
Level-2/Level-3 mockup already validated in brainstorming.)

**7.3 `parameter-range-binding.svg` (Section 3.3, B)**
Two side-by-side JSON-like cards (`parameters` and `ranges`) with the shared key
`"temperature"` highlighted in both and a connector showing the key is the glue.
A faint second pair hints how a second property adds a second matching key.

## 8. Content integration (in `formats/coveragejson.qmd`)

- Each new figure uses Quarto image syntax with a caption and a `#fig-` id,
  matching the existing `![...](../diagrams/coverage-as-function.svg){#fig-...}`
  pattern.
- The ASCII blocks the figures supersede are **removed** (Section 3.3 key-binding
  block; Section 4.3 fold block); surrounding prose is adjusted so it reads
  cleanly with the figure.
- `coverage-as-function.svg` is retained; the spreadsheet figure is added after
  the spreadsheet-mental-picture paragraph in Section 2.1.
- The out-of-scope ASCII grids (Sections 5.1, 5.2, 5.5) are left untouched.

## 9. Conventions update (`diagrams/README.md`)

Document, so the next diagram is reproducible: the B-bar (palette, light card,
Level-2 floor, vocabulary parity); the color/accessibility rules (Section 5,
including the fill-vs-stroke contrast rule and the no-color-alone rule); the
motion rules (Section 6, including CSS-in-SVG and the reduced-motion guard); and
the file/accessibility conventions (hand-coded SVG, `role`/`title`/`desc`,
kebab-case names in `diagrams/`).

## 10. Success criteria and verification

- All three diagrams render correctly in `quarto preview` on **both** the light
  and dark themes.
- The animated diagram freezes to its static frame under
  `prefers-reduced-motion: reduce`.
- Each SVG has `role="img"` + `<title>`/`<desc>`; labels match the prose terms.
- Each diagram passes a **grayscale check** and a **color-blindness simulator**
  (Chrome DevTools "Emulate vision deficiencies" or Sim Daltonism), confirming no
  meaning is lost.
- Meaningful strokes/borders meet >= 3:1; text meets >= 4.5:1 on the card.
- Replaced ASCII blocks are removed and the surrounding prose still reads well.
- `diagrams/README.md` documents the full matured pattern.
- The site still builds clean (`quarto render`) and the CI link check passes.

**Early verification spike (do first):** confirm that CSS `@keyframes` inside an
SVG actually animate when the SVG is embedded via Quarto's `![](...)` (`<img>`)
mechanism, *before* building all three diagrams. This is the one real technical
risk; if `<img>` embedding does not animate, fall back to inline-SVG embedding
for the animated diagram and note it in the conventions.

## 11. Risks

- **CSS-in-SVG animation through `<img>`** may not behave identically across
  browsers; mitigated by the early spike and the static fallback (worst case: the
  diagram is simply static everywhere, which is still B-quality).
- **Hero composition uncertainty** (spreadsheet-over-map): mitigated by a
  companion mockup pass before committing the SVG.
- **Scope creep** into the out-of-scope diagrams: explicitly deferred; resist
  upgrading them in this pass.

## 12. Files

- New: `diagrams/coverage-as-spreadsheet.svg`, `diagrams/row-major-grid.svg`,
  `diagrams/parameter-range-binding.svg`.
- Edited: `formats/coveragejson.qmd` (embed figures, remove superseded ASCII),
  `diagrams/coverage-as-function.svg` (teal stroke contrast fix),
  `diagrams/README.md` (conventions).
