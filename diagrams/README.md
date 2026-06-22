# Diagrams

Concept diagrams for the guides. Two lanes, by purpose:

## 1. Hand-authored "hero" SVGs

For graphics that *teach a core concept*. Hand-code the SVG and commit it as
source (it diffs cleanly and stays editable). These follow the **B-bar** and
optional **C motion layer** described below.

Inventory (all used in the CoverageJSON guide):

- `coverage-as-function.svg` -- a coverage is a function: domain -> range, with an
  amber **worked instance** (one position "here" threaded to its value). Section 2.1.
- `coverage-shared-domain.svg` -- one shared domain fanning to **multiple ranges**
  (the plural-`ranges` idea: one function per property, one domain). Section 2.1.
- `parameter-range-binding.svg` -- a shared key binds a parameter to its range.
  Section 3.3.
- `row-major-grid.svg` -- a flat `values` list folds onto a grid in row-major
  order; **animated** (C layer) with a static fallback. Section 4.3.

## 2. Text-based structural diagrams

For object trees, pipelines, and flows that change often: author as Mermaid,
Graphviz, or D2 and let Quarto render them to SVG at build time. These diff
cleanly in git. (None committed yet.)

---

## The B-bar: the static quality floor

Every hero SVG meets this bar before any motion is considered.

- **Palette.** Domain/inputs indigo `#6366f1`; range/outputs teal `#14b8a6`;
  neutral slate `#0f172a`; arrows/links slate-700 `#334155`. Amber `#f59e0b` is
  reserved for **highlight/motion only** -- never a category color.
- **Light card.** Wrap the diagram in a white (`#fff`) rounded rect with a
  hairline `#e2e8f0` border, so it reads on both the light (`cosmo`) and dark
  (`darkly`) site themes. (SVG `<img>` embeds don't get a theme-aware background,
  so the diagram supplies its own.)
- **An explanatory device.** A diagram must do more than label boxes: a worked
  instance, a bracket, a connector, a color-map -- something that *shows* the
  relationship, not just names it.
- **Vocabulary parity.** Reuse the guide's exact words in labels (domain, range,
  parameter, positions, values) so prose and picture say the same thing.
- **Accessibility metadata.** Every SVG has `role="img"` plus a linked `<title>`
  and `<desc>` (`aria-labelledby="… …"`). The `<desc>` describes what the figure
  shows, in the guide's vocabulary.
- **Even margins.** Pad the content evenly on all four sides and let the `viewBox`
  wrap it snugly -- no dead space, no lopsided gutters. Center sibling groups on a
  shared axis.

## Color and accessibility rules

- **Contrast: pastel fills, darker strokes.** Fills use pastel tints (`#eef2ff`,
  `#f0fdfa`, `#e0e7ff`, `#ccfbf1`); strokes, borders, and small text use the
  darker `#4338ca` (indigo-700) and `#0f766e` (teal-700). **Never** use light teal
  `#14b8a6` as a stroke or for small text -- it is only ~2.5:1 on white, below the
  3:1 non-text-contrast floor. (This is why `coverage-as-function.svg`'s range card
  stroke is `#0f766e`, not `#14b8a6`.)
- **No color-alone encoding.** Every categorical distinction is *also* carried by
  position, a text label, or a bracket/shape -- so it survives for color-blind
  readers. Verify with Chrome DevTools -> Rendering -> "Emulate vision
  deficiencies" (`Deuteranopia`).
- **Luminance separation.** Two category fills must differ in *luminance*, not only
  hue, or they collapse together in grayscale (achromatopsia). Indigo-100
  (`#e0e7ff`) and teal-100 (`#ccfbf1`) are both very light and nearly
  indistinguishable in grayscale; when two fills must read apart, deepen one (e.g.,
  the `row-major-grid.svg` "y0" row uses indigo-200 `#c7d2fe`). Verify with
  `Achromatopsia`.

## The C motion layer (optional)

Add motion **only where it teaches what a static frame cannot** (e.g., the
row-major fold walking value-by-value into the grid).

- **Mechanism.** CSS `@keyframes` inside a `<style>` block *within the SVG*. No
  JS, no SMIL.
- **Reduced-motion guard.** Always wrap the animation in
  `@media (prefers-reduced-motion: reduce) { … animation: none … }`.
- **Information parity.** The static frame must convey the full concept on its own;
  motion only re-illustrates what the static layer already shows. (This is also the
  safety net for the caveat below.)
- **Embedding.** Standard Markdown `![](…)` works -- CSS-in-SVG animations *do*
  run when the SVG is embedded via `<img>`. (Verified; no raw inline-SVG block
  needed.)
- **Reduced-motion caveat.** DevTools' "Emulate prefers-reduced-motion" override
  does **not** propagate into an `<img>`-embedded SVG -- it renders in its own
  document context. To verify the guard, open the `.svg` as a *top-level* document
  and emulate there; the real OS-level "Reduce motion" setting *does* reach the
  embedded `<img>`. Because some browsers may not propagate it, information parity
  is what guarantees the figure still works for a reduced-motion reader.

## Captions live in Quarto, not the SVG

Keep in-SVG text to **structural labels** (axis names, card titles, the
explanatory device). Put the **explanatory sentence in the Quarto figure caption**
(`![caption](…){#fig-…}`): it is real, selectable, accessible, searchable text,
and it gets the series' caption styling (smaller, muted, constrained measure, with
a bold "Figure N:" label) from `custom.scss` + `caption-labels.html`. Do not bake
a prose caption into the image.

## File conventions

- Hand-coded SVG, committed as source. Kebab-case filenames in `diagrams/`.
- Verify well-formedness with `xmllint --noout diagrams/<file>.svg` before
  committing.
- Embed with Quarto image syntax and a stable `#fig-…` id; reference figures by
  that id so cross-references and the link check resolve.
