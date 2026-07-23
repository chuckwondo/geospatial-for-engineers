# ADR-0002: Visual redesign: Survey Console, Terra Rusty, OFL fonts

## Status

Accepted. Implementation is issue #6.

## Context

The site chrome is stock Quarto (`cosmo` light, `darkly` dark) and does not match
the craft of the hand-authored teaching diagrams: issue #6. The brief was a full,
from-scratch redesign of the whole visual system, not an incremental adoption of
the diagram palette. The register, in the author's words: bold and expressive
first, disciplined by analytical precision and crispness, generous breathing
room, supremely readable, and it must feel complete and self-contained. The
chrome should carry the series' framing (teach the domain, trust the reader's
code) rather than sit neutrally around it.

Three constraints shape the choice. This is a public open-source repository (MIT
code, CC BY 4.0 prose and diagrams). Quarto is the confirmed engine, on the
merits: the pivotal axis is build-time reproducible content, which the COG guide
will need and which is the hardest property to replace. And WCAG AA is the
author's responsibility, because Quarto has no built-in contrast gate, so the
design must hold AA by construction.

The decisions were made against rendered, full-content mockups in both themes,
judged by eye and checked with adversarial contrast verification. The chosen
mockup is preserved at
[`assets/0002-visual-redesign-mockup.html`](assets/0002-visual-redesign-mockup.html)
and is the source of truth for every exact value this ADR summarizes.

## Decision

**Direction: "Survey Console"** (a terminal-and-cartographic hybrid). The site
reads as a developer's console that is also a surveyor's field instrument: a
`~/geo $` prompt and a terminal-window frame recur as the chrome for the hero,
code blocks, cards, and diagram figures, and a faint graticule, contour rings,
and coordinate ticks wash the page. The signature move is that the terminal
cursor is the map cursor: the left "field book" sidebar carries a live `lat /
lon / crs / t` readout, and a static (non-blinking) block cursor closes the hero.
One display-serif line (the hero thesis) is the single expressive peak;
everything else is a readable sans for body and mono for UI, prompts, headings,
and code.

**Palette: "Terra Rusty"** (green). A warm, earth-forward scene read as one
coherent landscape, not a rainbow: a warm greyed-green paper in light and a deep
pine near-black in dark (both designed from scratch, not naive inverts), one
dominant terracotta accent, a sky-blue secondary, and a forest-green support hue.
Warmth comes from an iron-oxide red undertone, never yellow. The load-bearing
tokens, so this record stands alone if the mockup is lost:

- Light: `--bg #dee9d7`, `--ink #1b241a`, accent `--accent #ac4318` (text) and
  `--accent-ui #c85a2a` (fills), secondary `--teal #175f96`, support `--olive
  #34632f`.
- Dark: `--bg #0d190f`, `--ink #e0eade`, accent `#e8895a`, secondary `#5db4e0`,
  support `#7bc680`.

Terracotta is the one dominant accent (links, prompts, active nav, cursor);
sky-blue is secondary; forest-green is support. The token names `--teal` and
`--olive` are kept deliberately even though they now hold the sky-blue and the
forest-green, because renaming them ripples through every component and the
diagram tokens.

**Typography: self-hosted OFL fonts only.** Fraunces for the one hero serif line,
Inter for body sans, JetBrains Mono for all UI and code. All three are Open Font
License and self-hosted; no paid fonts.

**Layout and ambient field.** Content fills the column (`--measure: min(120ch,
100%)`, `--shell: 1760px`), with no narrow center band and empty gutters. A
whole-page ambient "satellite field" (an inline, palette-token-driven SVG of
blurred land-cover patches, a graticule, and contour rings) sits behind the
entire page and repaints per theme; reading-lane and chrome scrims (washes in
`--bg`) buy back contrast so text stays past AA while the field stays visible.
The full token set, the component catalog, and the exact field and scrim values
live in the mockup; the tokens above are the summary.

## Alternatives considered

- **The blue "Tidewater" palette twin.** Built as a full second mockup, identical
  in every way but the palette, and compared side by side. Rejected: green read
  richer to the author's eye, while blue leaned on brightness for its land-water
  separation and came across cooler and flatter. Tidewater is recoverable from
  its own artifact if the choice is ever revisited.
- **Paid display or mono fonts (Söhne, Berkeley Mono).** Rejected on two grounds.
  Self-hosting a paid font in a public repository means committing and serving the
  font file, which redistributes it and violates its license; and none is
  load-bearing, because the OFL trio fully realizes the look. Every font routes
  through a `--serif` / `--sans` / `--mono` token, so a paid mono can be swapped
  in behind the token later if it is ever licensed in a compliant way: a two-way
  door.
- **A reading-column-only ambient field, or a Canvas procedural terrain.**
  Rejected. Whole-page scope read as more complete and deliberate; a Canvas
  terrain read "too invisible" in testing. The SVG field is palette-token-driven,
  so it repaints per theme for free, which a raster or Canvas scene would not.
- **Incremental adoption of the diagram palette, keeping the stock chrome.**
  Rejected. The brief was explicitly a complete visual system that feels
  self-contained; a partial reskin over stock `cosmo` and `darkly` would not
  clear that bar.

## Consequences

- The exact realization is the preserved mockup; treat it as the source of truth,
  and update this ADR only if a *decision* changes, not to track token tweaks.
- Quarto implementation caveats to honor, the highest-risk one first:
  1. Quarto website projects cannot override template partials, so the terminal
     masthead and cursor sidebar are built by *restyling* the navbar and sidebar
     skeleton (via header or before-body includes, or by styling the Bootstrap
     structure), not by rebuilding it. Validate this chrome early; it is the
     biggest unknown.
  2. Use `brand.yml` (Quarto 1.7+) for the palette and the paired light and dark
     themes.
  3. Self-hosted fonts have a known asset-path gotcha (quarto-cli #9518).
  4. A hand-authored or inline SVG needs a raw-HTML include, not `![](file.svg)`.
  5. `freeze` caches on source hash, not dependency versions, so once executable
     guides land, a CI gate must re-render when `uv.lock` changes.
  6. WCAG AA is un-gated by Quarto, so it is verified by hand, in both themes,
     after the port (surfaces and opacities shift in translation).
- The teaching diagrams are a separate, coupled decision:
  see [ADR-0003](0003-teaching-diagrams-theme-adaptive.md).
- Implementation (#6) is staged for review: the chrome, palette, fonts, and the
  diagram-inlining mechanism first, then the diagram conversion, and the live
  site is not deployed until the whole set is coherent.
- **Verified in implementation (#6).** The spike and build confirmed the recipes
  below and refined the caveats above:
  1. *Self-hosted fonts (#9518):* put `@font-face { src: url("fonts/x.woff2") }`
     in the theme SCSS with **no** `project.resources` entry. Quarto then copies
     the font adjacent to the compiled CSS (`site_libs/bootstrap/fonts/`), so the
     bare url resolves at any page depth and under the GitHub Pages subpath.
  2. *The dark signal is `body.quarto-dark`, not `[data-theme]`.* Quarto swaps two
     Bootstrap bundles, so one shared `custom.scss` carries `:root { light }` plus
     `body.quarto-dark { dark }`, compiled into both. Any token whose value is a
     `color-mix`/`var(--bg)` (the scrims) must be redeclared inside
     `body.quarto-dark`, or it bakes the light value.
  3. *`brand.yml` was evaluated and not used* (refines caveat 2). A single shared
     `custom.scss` carries the full bespoke token set (`--dg-*`, `--panel-*`,
     `--scrim-*`, the type scale) more simply than brand.yml's curated,
     Bootstrap-facing subset.
  4. *Diagrams inline via a `::: {#fig-id}` div wrapping a `{=html}` block*
     (refines caveat 4): this keeps the `@fig-` cross-reference and lets the
     `--dg-*` tokens resolve against the page. See
     [ADR-0003](0003-teaching-diagrams-theme-adaptive.md) and `diagrams/README.md`.
  5. *Full-width content needed a docked page-grid override* (the body track made
     fluid, the trailing outer gutter collapsed); `page-layout: full` is a no-op
     against Quarto's explicit grid.
  6. *WCAG AA holds in both themes* (hand-checked): every body/label pair clears
     4.5:1 and every UI/stroke pair clears 3:1, including the `--dg-*` diagram
     tokens.
