# ADR-0003: Teaching diagrams as theme-adaptive, token-driven inline SVGs

## Status

Accepted. Implementation is issue #6, which absorbs issue #12.

## Context

The series teaches with 15 hand-authored "hero" SVG diagrams under `diagrams/`.
Every one hardcodes its colors as hex literals, with zero CSS variables. They do
follow a consistent semantic palette (indigo for the domain, teal for the range,
slate for ink and neutrals, amber for the worked instance), expressed as
multi-shade ramps; but because the colors are baked in, the diagrams cannot
theme-switch, and render the same palette regardless of light or dark.

The redesign (ADR-0002) introduces a deep-pine dark theme and a whole design
system built on palette tokens, and expects diagrams to repaint through a
`--dg-*` token set. Against the new near-black dark ground an unconverted diagram
would render its old pale-indigo-on-white palette: not merely dated, but broken.
Separately, issue #12 is a legibility defect: on narrow screens the wide diagrams
scale down until their labels shrink to roughly five pixels.

The site is one guide deep, so all 15 diagrams live in the single published
guide.

## Decision

Re-author all 15 diagrams as theme-adaptive, token-driven SVGs, and do the whole
set within issue #6. Each diagram's hardcoded hex ramps become `var(--dg-*)`
tokens at stepped opacities (the pattern the re-authored `coverage-as-function`
exemplar in the mockup proves), so it repaints per theme; the series' existing
rule holds, that a role is distinguished by luminance, position, shape, and
label, never by color alone. The SVGs are inlined via a raw-HTML include, because
an SVG referenced with `<img src>` is isolated from the page and cannot inherit
its CSS variables. The #12 mobile-legibility fix is folded into the same pass,
since every SVG is being rewritten anyway.

Delivery is staged: the chrome plus a few converted diagrams to lock the pattern
first, then the rest converted systematically. The live site is not deployed
until the set is coherent, so no half-converted state ships.

## Alternatives considered

- **Convert only the hero diagrams now, the rest as they are later touched.**
  Rejected. On a one-guide site there is no "later touch" that would ever fire, so
  the remainder would sit unconverted indefinitely; and an unconverted diagram
  does not read as merely old against the new dark theme, it reads as broken (old
  light palette on a near-black ground), which fails the "feels complete" bar on
  a live public URL.
- **Keep the SVGs as `<img src>` files and recolor them per theme with CSS
  filters, or ship one file per theme.** Rejected. An img-embedded SVG cannot see
  the page's CSS variables, so it cannot repaint cleanly through the token system;
  per-theme duplicates double the maintenance for every future edit. Inlining with
  tokens keeps one source of truth that adapts.
- **Leave #12 as an independent issue, fixed separately from the redesign.**
  Rejected as the default path, because the conversion touches every SVG and so
  fixes #12 for free. It is noted, though, that #12 was filed deliberately
  separate so a legibility defect would not be held hostage to a large redesign:
  if #6 stalls, #12's fix is separable and can ship on its own.

## Consequences

- The 15 diagrams move from committed, single-palette `.svg` assets to inlined,
  token-driven markup that adapts to both themes.
- `diagrams/README.md` is updated to document the `--dg-*` token mapping in place
  of the old hardcoded palette.
- AA is re-verified in both themes after conversion, because the token opacities
  and the new surfaces shift the effective contrast.
- The work closes #12 alongside #6. It is systematic, not 15 bespoke redesigns (a
  shared semantic palette plus one proven exemplar), but it remains real
  per-diagram handwork: remap each ramp, fix the mobile scaling, and re-check
  contrast.
