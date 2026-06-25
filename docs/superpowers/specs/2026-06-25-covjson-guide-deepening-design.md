# Design: deepening the CoverageJSON guide (edge cases, tiling, forward-links)

**Date:** 2026-06-25
**Status:** Approved (brainstorm)
**Artifacts:**
- `formats/coveragejson.qmd` — content additions + structural refactor.
- New diagrams: `diagrams/tiled-ndarray.svg`, `diagrams/external-references.svg`,
  `diagrams/axis-bounds.svg`, `diagrams/coverage-collection-hoist.svg`,
  `diagrams/categorical-encoding.svg`.
- New stub page: `tutorials/index.qmd` (+ `_quarto.yml` sidebar entry).
- `_quarto.yml` — `number-sections: true`.

## Purpose

The CoverageJSON guide (`formats/coveragejson.qmd`) is the series' first complete
guide and is already mature: six sections, eleven validated diagrams, every JSON
example checked against the OGC schema. This effort fills the remaining *pockets*
— thin detail and missing visuals — rather than restructuring. Two themes drive
it:

1. **Completeness of understanding.** A reader who works through the guide
   thoroughly should be left with no unanswered *conceptual* questions — only
   hands-on ones, which forward-links hand off to tutorials / real-world-example
   guides (to be written).
2. **Cross-guide bridges.** The guide is the natural place to connect
   CoverageJSON's tiling/lazy-loading to the chunking ideas the future Zarr and
   COG guides will cover. Engineers already know chunking; this maps it onto
   CovJSON.

## Goals

- Expand `TiledNdArray`/`TileSet` from one paragraph into a full treatment with a
  validated JSON example and a diagram (the Zarr/COG bridge).
- Cover the edge cases the guide currently skips or under-explains: external URL
  references, axis `bounds` (cell-extent vs point), `parameterGroups`,
  multi-code category encoding, and domains without `domainType`.
- Add the three missing diagrams the prose already implies (collection hoisting,
  categorical encoding) plus the new-content diagrams.
- Establish a CI-safe **"Going further"** forward-link convention backed by a
  `tutorials/` stub area.
- Adopt Quarto native section numbering + `@sec-` cross-references to retire the
  manual-renumbering hazard permanently.

## Non-goals

- No new *worked examples* in §5 for the edge cases (they live inline in §4).
- No rewrite of §1–§3; only targeted insertions where noted.
- No `formats/cog.qmd` stub — COG is linked externally (cogeo.org).
- Not writing the tutorials themselves — only the stub landing page + anchors.
- No move to the `developmentseed` org or any publishing change.

## Cross-cutting conventions

**"Going further" pattern.** Each meaty new/expanded subsection ends with a short
pointer block offering up to three links: (1) the precise **spec section** (live
external, normative depth — the guide's existing habit); (2) a **live hands-on
resource** where one exists (covjson.org, the Playground, a real OGC API -
Coverages endpoint); (3) an **in-repo tutorial stub** for the deep how-to we
intend to write.

**Stub mechanism (CI-safe).** One new page, `tutorials/index.qmd` — "Tutorials &
real-world examples (planned)" — carries an **H2 per planned deep-dive**.
Forward-links point at `../tutorials/index.qmd#sec-slug` (or the heading's
auto-id). The CI link-check is `lychee --offline --include-fragments`: external
`http(s)` links are skipped (so spec/covjson.org/OGC-API links can never break
CI), but **internal relative links and `#fragment` anchors are verified** against
the rendered `_site`. Therefore every internal forward-link must resolve to a
heading that exists — which the stub guarantees. The stub mirrors the existing
`foundations/geospatial-concepts.qmd` and `formats/zarr.qmd` "planned" pattern.

**Tie-ins to future guides.** Zarr chunking → internal link to the existing
`formats/zarr.qmd` stub. COG tiling/overviews → live external link to cogeo.org.
Foundations (CRS, resolution/cells) → internal links to the existing
`foundations/geospatial-concepts.qmd` stub.

**Diagram + validation discipline.** New SVGs follow `diagrams/README.md` (indigo
domain/inputs, teal range/outputs, slate neutral; B-bar; animation only where it
teaches; figure captions reusing the guide's vocabulary verbatim). Every new JSON
example is validated against the OGC CoverageJSON JSON Schema before commit.

## Step 1 — Quarto auto-numbering refactor (standalone, first)

Done as its own commit, before any content work, so future inserts never trigger
a manual renumber and cross-references can never silently drift. This pass also
retitles the guide (it has outgrown "A Beginner's Guide").

1. Set `number-sections: true` in `_quarto.yml` (`format: html:`).
2. Strip the manual numbers from every heading in `coveragejson.qmd`
   (`### 4.3 How ranges…` → `### How ranges… {#sec-ndarray}`).
3. Add a `sec-`-prefixed id to every heading (required for section
   cross-references).
4. Convert every prose "Section X.Y" reference to `@sec-…`, which renders at
   build time as a hyperlinked "Section N.M" with the current number. Unresolved
   refs surface as `?@sec-…` build warnings — a strictly better safety net than
   the manual `#fragment` check.
5. Audit the figure captions and callouts, which also contain "Section X.Y"
   prose references.
6. Retitle the guide in its front matter — keeping the engineer pedagogy intact,
   with the subtitle leading on completeness rather than audience (the audience
   framing already lives in the series title and the opening paragraph):

   ```yaml
   title: "CoverageJSON Uncovered"
   subtitle: "A complete, ground-up guide to reading coverage data on the web — every concept, from your first document to the edge cases."
   ```

**Verification for Step 1:** `quarto render` produces no `?@sec-` warnings;
spot-check that rendered numbers match the previous manual numbers; lychee
offline passes.

## Step 2 — content additions

All placements assume Step 1's `{#sec-…}` ids. "Lands in" names the current
section; exact ordering of inserted subsections is in the implementation plan.

| Item | Lands in | Depth | Diagram | Going-further targets |
| ---- | -------- | ----- | ------- | --------------------- |
| **TiledNdArray / TileSet** | Expand §4.3 | Full: why tile → `TileSet` (`tileShape`, `urlTemplate`) → **multiple tile sets per array** (spatial vs temporal chunking; client picks) → validated JSON | `tiled-ndarray.svg` | spec 9.6.3 · Zarr stub · cogeo.org · tutorial stub |
| **External URL refs (E1)** | New subsection after §4.3 | Full: `domain` and each `ranges` entry may be a URL string; boundary (whole object only, never per-axis values); cashes the §3.1 `type` promise; "two levels of fetch-on-demand" | `external-references.svg` | spec 9.6.4 · OGC API - Coverages · tutorial stub |
| **Axis `bounds` (E2)** | Into §4.2.1 | **Upgraded — see below** | `axis-bounds.svg` | spec 9.6.1 · foundations stub · GDAL PixelIsArea |
| **CoverageCollection diagram (#2)** | §4.5 + §5.6 | Diagram only (prose exists) | `coverage-collection-hoist.svg` | — |
| **Categorical-encoding diagram (#3)** | §4.4 + §5.5 | Diagram of codes → legend → labels; supersedes §5.5 ASCII | `categorical-encoding.svg` | — |
| **`parameterGroups` (E3)** | Into §4.4 | Minimal-complete: what it groups, wind u/v motivation, how to read one; snippet | — | tutorial stub (vectors) |
| **Multi-code category (E4)** | Into §4.4 | Minimal: one category → several codes (`"cloud": [3,4,5]`), when/why; 2-line snippet | — | — |
| **No `domainType` (E5)** | Into §4.1 | Small real paragraph (see below) | — | tutorial stub (custom axes) |

### E2 (axis `bounds`) — upgraded treatment

This is a genuine trap, not just a missing feature (Chuck was recently tripped up
computing a cell midpoint), so it gets prominence:

- **Core idea, plainly:** in a gridded coverage each axis coordinate is a single
  number, but the cell it labels covers an *interval*. The coordinate is only a
  **representative point** for that cell — and you cannot assume it sits at the
  cell **center**; it may be a center or an edge.
- **The trap, as a `callout-caution`:** guess the wrong convention and every
  position shifts by half a cell — the classic **half-pixel shift**. The midpoint
  computation is exactly where it bites: `midpoint = (lower + upper) / 2` is only
  correct if you know the cell's extent; assuming coordinates are centers when
  they are edges puts you off by half a cell everywhere.
- **What `bounds` does:** an axis may carry a `bounds` array giving the explicit
  `[lower, upper]` extent of each cell (two numbers per coordinate), removing the
  guesswork — the midpoint is unambiguous and you never infer center-vs-edge.
- **Engineer tie-in:** the same distinction GeoTIFF encodes as **PixelIsArea vs
  PixelIsPoint**, and the origin of a family of half-pixel georeferencing bugs in
  GDAL/raster tooling. Naming it lets the reader file it next to a bug they may
  already have hit.
- **`axis-bounds.svg`:** a coordinate tick bracketed by its `[lower, upper]` cell
  extent with the midpoint marked, and the center-vs-edge interpretations side by
  side so the half-cell shift is *visible*.

### E5 (no `domainType`) — paragraph content

A domain need not declare a `domainType`; the keyword is a convenience that lets
generic software recognize a known shape without inspecting the axes. A domain
without it is still fully valid and self-describing via `axes` + `referencing`;
you read it by inspecting which axes are present, single- vs multi-valued,
individual vs composite. You omit it when your data varies along a dimension the
standard types don't anticipate — a **wavelength** axis (hyperspectral), an
**ensemble/realization** axis (probabilistic forecasts), a **band** axis
(multispectral). This quietly teaches that **axis names are not limited to
`x/y/z/t`** (the reason the escape hatch must exist); a custom axis needs a
`referencing` entry so its numbers mean something. Include a tiny snippet with a
custom axis and no `domainType`.

## Step 3 — diagrams (maximal scope: all five)

All "must," per Chuck's "maximally scope" call:

1. `tiled-ndarray.svg` — one logical array split into tiles, each a URL; show two
   tile sets (different chunk shapes over the same array). The Zarr bridge.
   Animation if it clarifies tile fetching.
2. `external-references.svg` — the two deferral levels: whole-object URL
   (`domain`/`ranges` as a string) vs tile-level URL (`urlTemplate` inside a
   `TiledNdArray`).
3. `axis-bounds.svg` — coordinate point vs cell extent + midpoint + half-cell
   shift (see E2).
4. `coverage-collection-hoist.svg` — shared `parameters`/`referencing` hoisted to
   the collection level; each member coverage carries only its own domain+range.
5. `categorical-encoding.svg` — integer grid → `categoryEncoding`/`categories`
   legend → decoded label grid.

## Step 4 — tutorial stubs

Create `tutorials/index.qmd` ("Tutorials & real-world examples (planned)", a
"planned" stub like the existing ones) with an H2 (each `{#sec-…}`) per planned
deep-dive, and add a "Tutorials" sidebar section to `_quarto.yml`. Initial
anchors:

- Serving & consuming a `TiledNdArray` (tiling, `urlTemplate`, multiple tile
  sets).
- External references & lazy loading via OGC API - Coverages.
- Non-standard domains & custom axes (ensembles, hyperspectral).
- Vector quantities with `parameterGroups`.

## Sequencing

1. **Step 1** — Quarto auto-numbering refactor (own commit).
2. **Step 4** — tutorial stub + sidebar (so forward-links resolve as content is
   added).
3. **Step 2/3 content+diagrams**, in value order: TiledNdArray → external refs →
   collection diagram → categorical diagram → bounds → E3/E4/E5.
4. Final verification.

## Verification

- `quarto render` is clean: no `?@sec-` unresolved-reference warnings, no render
  errors.
- `lychee --offline --include-fragments` passes locally over `_site`; every new
  internal forward-link anchor resolves.
- Every new JSON example validates against the OGC CoverageJSON JSON Schema.
- New diagrams render correctly in **both** light (`cosmo`) and dark (`darkly`)
  themes.
- `prek run --all-files` is clean.

## Notes

- Chuck commits himself; this effort stages changes only (per CLAUDE.md), so the
  spec is written + staged but not committed by the agent.
