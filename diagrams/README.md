# Diagrams

Concept diagrams for the guides. Two lanes, by purpose:

## 1. Hand-authored "hero" SVGs

For the graphics that *teach a core concept* (e.g., "a coverage is a function
from a domain to a range"). Commit the `.svg` directly. Keep the editable source
alongside when the tool supports it (e.g., an Excalidraw `.svg` can embed its own
scene so it can be re-opened and edited).

- `coverage-as-function.svg` -- a coverage maps a domain (positions) to a range
  (values). Used in the CoverageJSON guide.

## 2. Text-based structural diagrams

For object trees, pipelines, and flows that change often: author as Mermaid,
Graphviz, or D2 and let Quarto render them to SVG at build time. These diff
cleanly in git.

## Style conventions (keep the series coherent)

- **Palette:** domain/inputs in indigo (`#6366f1`), range/outputs in teal
  (`#14b8a6`), neutral text in slate (`#0f172a`), arrows/links in slate-700
  (`#334155`).
- **Cards:** give a diagram a light rounded-rect background so it reads on both
  light and dark site themes.
- **Labels:** reuse the guides' vocabulary verbatim (domain, range, axis, CRS,
  parameter) so a reader sees the same words in prose and picture.
