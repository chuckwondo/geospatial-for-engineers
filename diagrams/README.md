# Diagrams

Concept diagrams for the guides. Two lanes, by purpose:

## 1. Hand-authored "hero" SVGs

For graphics that *teach a core concept*. Hand-code the SVG and commit it as
source (it diffs cleanly and stays editable). Each is **theme-adaptive**: its
colors come from `--dg-*` custom properties (see below), so the same file
repaints for the light and dark site themes. The `.svg` file is the editable
source; its body is also **inlined** into the guide (see Embedding) so the
tokens resolve and the figure gets a Quarto cross-reference.

Inventory (all used in the CoverageJSON guide):

- `coverage-as-function.svg` -- a coverage is a function: domain -> range, with a
  terracotta **worked instance** (one position "here" threaded to its value).
  Section 2.1.
- `coverage-shared-domain.svg` -- one shared domain fanning to **multiple ranges**
  (the plural-`ranges` idea: one function per property, one domain). Section 2.1.
- `parameter-range-binding.svg` -- a shared key binds a parameter to its range.
  Section 3.3.
- `row-major-grid.svg` -- a flat `values` list folds onto a grid in row-major
  order, read from the color-coded rows and the "x varies fastest" cue. Section 4.3.
- `pointseries-daily-curve.svg` -- a PointSeries as a daily temperature curve
  (time = domain, values = range; the noon peak spotlit as the instance). Section 5.2.
- `gridded-axes.svg` -- two individual `x`/`y` axes cross-product into a full
  regular 2x3 lattice: the gridded case. Section 4.2.1.
- `axis-bounds.svg` -- the same 2x3 lattice as `gridded-axes.svg`, with two candidate
  grids laid over it: read as cell centers, the shaded cells box each position; read
  as cell corners, the dashed lines cross at them. The two grids sit half a cell apart
  along both axes, which is the half-pixel trap (GeoTIFF's `PixelIsPoint` vs
  `PixelIsArea`) that `bounds` exists to settle. Section 4.2.1.
- `non-gridded-composite.svg` -- three points at irregular, off-lattice positions
  over a faint reference grid (sharing no `x`/`y` values to factor), so a
  `composite` axis lists each as a tuple: the non-gridded case. Section 4.2.2.
- `polygon-composite-axis.svg` -- a polygon as a list of rings: an irregular outer
  boundary with a triangular inner ring punched out as a hole. Section 4.2.
- `domain-types-gallery.svg` -- the common domain types grouped by axis structure:
  individual-axis types (`Point`, `PointSeries`, `VerticalProfile`, `Grid`) vs
  composite-axis types (`MultiPoint`, `Trajectory`, `Polygon`, `MultiPolygon`).
  Section 4.1.
- `multipointseries-through-time.svg` -- a `MultiPointSeries` as three side-by-side
  frames: the dots (positions) stay fixed while their value labels change with
  `t`, with one station threaded by dashed arrows. Shows what a `...Series` adds.
  Section 4.1.
- `section-curtain.svg` -- a `Section` as the equation `Trajectory` x
  `VerticalProfile`: a composite `(t, x, y)` axis (listed as tuples) crossed with a
  `z` depth axis fills a curtain of values, with one worked station highlighted
  across the track, the list, and the curtain. Section 4.2.

## 2. Text-based structural diagrams

For object trees, pipelines, and flows that change often: author as Mermaid,
Graphviz, or D2 and let Quarto render them to SVG at build time. These diff
cleanly in git. (None committed yet.)

---

## The `--dg-*` token system

Every hero SVG is painted from a small set of custom properties defined for both
themes in [`_theme/_tokens.scss`](../_theme/_tokens.scss). Because the values are
CSS variables, one file repaints for light and dark automatically; the palette is
never baked into the diagram.

| Role | Token |
| --- | --- |
| Panel / surface fill | `--dg-panel` |
| Titles, primary ink | `--dg-ink` |
| Axis lines, secondary labels | `--dg-line` |
| Faint borders / graticule | `--dg-graticule` |
| Domain stroke / fill | `--dg-domain` |
| Domain text | `--dg-domain-ink` |
| Range stroke / fill | `--dg-range` |
| Range text | `--dg-range-ink` |
| Worked-instance stroke / fill | `--dg-instance` |
| Worked-instance text | `--dg-instance-ink` |

Rules for using them:

- **CSS variables only work in a `style` attribute, not a presentation
  attribute.** Write `style="fill:var(--dg-range)"`, never `fill="var(--dg-range)"`
  (the latter is invalid and renders black/transparent). Combine fill and stroke
  in one `style`: `style="fill:var(--dg-panel);stroke:var(--dg-domain)"`.
- **Opacity ramps stay on one token.** For a value ramp or a stacked depth cue,
  vary `fill-opacity` (or blend toward another token with `color-mix`) on a single
  hue; never introduce a new color.
- **Map by role, not by old hex.** Converting a pre-token diagram, match each
  element to its *role* above rather than to the raw color it used to be.

## Accessibility rules

- **Roles differ by luminance + position + shape + label, never color alone.**
  Every categorical distinction is also carried by where it sits, its shape, or a
  text label, so it survives grayscale and color-blind vision. Verify with Chrome
  DevTools -> Rendering -> "Emulate vision deficiencies" (`Deuteranopia`,
  `Achromatopsia`). The token luminances are ordered so domain/range/instance stay
  separable without color; keep them so.
- **Refer to elements by role, never by color.** In-figure keys, axis text, and
  the `<desc>` must name elements by an unambiguous structural handle (e.g., "the
  dots", "the value label", "the highlighted station with the heavier outline"),
  not by fill ("the teal dots", "the terracotta ring"). Color names do not survive
  grayscale, fail color-blind readers, and break when the palette changes.
- **Accessibility metadata.** Every SVG has `role="img"` plus a linked `<title>`
  and `<desc>` (`aria-labelledby="… …"`). The `<desc>` describes what the figure
  shows, in the guide's vocabulary. Because 15 diagrams share one page, **prefix
  every internal id per diagram** (title/desc, markers, patterns, gradients) with a
  short slug (`cf-`, `sec-`, `tt-`, …) and update every `url(#…)` and
  `aria-labelledby` reference, or the ids collide across figures.
- **Contrast is a hard gate, both themes.** Diagram label text clears WCAG AA
  (>=4.5:1) and role strokes clear >=3:1 against `--dg-panel`, in light and dark.
  Re-check after any token change.

## Typography

Diagram text carries **no `font-family`**: it inherits the mono UI face through
the `svg.dg text` rule in [`_theme/_content.scss`](../_theme/_content.scss). Mono
glyphs are wider than a proportional face, so when converting or authoring, keep
labels short and let the `viewBox` (and the inner panel rect, sized to match) wrap
the content with even margins; verify nothing clips at the panel edge.

## Motion is deferred

The site currently ships a **static substrate**. Diagram motion is a deliberate,
later pass (see the `diagram-interactivity-deferred` decision), designed as one
consistent language across the set rather than per-diagram one-offs. Two diagrams
(`section-curtain`, `row-major-grid`) previously carried ad-hoc CSS sweeps; they
are frozen to their static resting state, and the sweep logic remains in git
history as a starting point for that pass. Do not add new in-SVG animation here
until then.

## Captions live in Quarto, not the SVG

Keep in-SVG text to **structural labels** (axis names, card titles, the
explanatory device). Put the **explanatory sentence in the Quarto figure caption**:
it is real, selectable, accessible, searchable text, and it gets the series'
caption styling (smaller, muted, constrained measure, with a bold "Figure N:"
label) from `custom.scss` + `caption-labels.html`. Do not bake a prose caption
into the image.

## Embedding

Diagrams are **inlined**, not linked as images, so the `--dg-*` tokens resolve
against the page. In the guide, wrap the SVG body in a Quarto cross-reference div
with a raw-HTML block:

````markdown
::: {#fig-example .diagram}
```{=html}
<div class="diagram-frame"><span class="dollar">$</span> render example.svg<span class="spacer"></span><span class="coord">short hint</span></div>
<div class="diagram-svg-wrap">
<svg viewBox="…" role="img" aria-labelledby="ex-title ex-desc" class="dg"> … </svg>
</div>
```
The explanatory caption sentence.
:::
````

Quarto renders this as a numbered figure (`@fig-example` resolves), and
`.diagram`/`svg.dg` styling in `_theme/_content.scss` supplies the terminal frame,
scrolling wrap, and caption. Keep the `.svg` file and the inlined copy in sync.

## File conventions

- Hand-coded SVG, committed as source. Kebab-case filenames in `diagrams/`.
- Verify well-formedness with `xmllint --noout diagrams/<file>.svg` before
  committing.
- Give the `<svg>` `class="dg"`, a stable `#fig-…` id on the wrapping div, and a
  per-diagram id prefix; reference figures by that id so cross-references and the
  link check resolve.
