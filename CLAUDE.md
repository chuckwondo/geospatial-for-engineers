# CLAUDE.md

Context for Claude Code working in this repo. This project was bootstrapped in a
conversation that started in another repo (`developmentseed/titiler-covjson`,
where the CoverageJSON guide was first written); this file carries that context
over so work can continue here.

## What this project is

**Geospatial for Engineers** -- a [Quarto](https://quarto.org/) static-site
series of beginner's guides to geospatial and earth-observation (EO) data,
written for **experienced software engineers with zero domain background**.

The defining framing (the gap this fills): most geospatial tutorials assume the
*domain* knowledge and explain the *code*. This series inverts that -- it assumes
code/CLI/JSON/data-structure fluency and spends its words on the domain (CRS,
projections, raster vs. vector, coverages, footprints, COG, Zarr).

Personal ramp-up project by Chuck Daniels (chuck@developmentseed.org). Dev Seed
does heavy NASA/EO work and maintains the rio-tiler / TiTiler stack. May later
move to the `developmentseed` org; for now it lives under `~/src/chuckwondo`.

## Audience & voice

- Reader: seasoned software engineer, new to geospatial/EO. Trust their code
  fluency; teach the domain.
- Patient, example-driven, **diagram-forward** (engaging visuals are a core goal,
  not an afterthought).
- Set expectations honestly: these are practical on-ramps, not exhaustive
  treatments; point to the specs for depth.

## Tech & build

- Engine: **Quarto** (website project). Quarto 1.9.38 is installed (via
  `brew install --cask quarto`).
- `quarto preview` for live local view; `quarto render` builds into `_site/`.
- `_quarto.yml`: docked sidebar (Foundations / Data formats), light (`cosmo`) +
  dark (`darkly`) themes, search. `render` is restricted to `*.qmd` so
  `CLAUDE.md`/`README.md` are not turned into pages.
- **No Python environment yet.** Every page is currently prose and renders with
  no kernel, so `environment.yml` was removed (YAGNI). When the first
  *executable* guide gets real code, add the env then -- likely **uv**
  (`pyproject.toml` + `uv.lock`): the planned libs (xarray, rio-tiler, zarr,
  virtualizarr, etc.) are all pip-installable, and uv gives a lockfile,
  lockfile-pinned CI, and Dependabot-tracked deps (conda has none of these).
- **CI/CD & hygiene** (`.github/` + repo root): `ci.yml` renders the site then
  runs an offline lychee link-check; `publish.yml` deploys to GitHub Pages via
  OIDC (no `gh-pages` branch, no secret); `dependabot.yml` does weekly
  github-actions bumps; `.pre-commit-config.yaml` (+ `scripts/pin-precommit-shas.sh`),
  `.editorconfig`, `.gitattributes` cover local hygiene. All Actions and
  pre-commit revs are SHA-pinned with tag comments; run `prek run --all-files`.

## Layout

- `index.qmd` -- landing page + the framing.
- `foundations/geospatial-concepts.qmd` -- the spine (stub; write this early).
- `formats/` -- `coveragejson.qmd` (complete), `zarr.qmd` (stub).
- `diagrams/` -- concept diagrams + their conventions (`diagrams/README.md`).
- Library guides were removed for now (which ones to cover is undecided); a
  `libraries/` section returns once that's settled.

## Prose style (carried from the CoverageJSON guide)

- Spell out acronyms on first use with brief motivation.
- **Avoid the `--` dash; prefer other punctuation.** Default to a colon (for a
  definition or lead-in), parentheses (for an aside), a comma (for an appositive),
  or splitting into two sentences. Reserve `--` for the rare case where nothing
  else reads as well; if a dash is truly needed use `--`, never a literal em dash
  (`—`). When editing, treat an existing `--` as a smell: try the alternatives
  first.
- `e.g.,` / `i.e.,` with a trailing comma; write "Section" not the section symbol.
- Cite specs precisely: canonical document id + a deep link to the exact heading
  anchor (CovJSON = OGC 21-069r2,
  [docs.ogc.org/cs/21-069r2](https://docs.ogc.org/cs/21-069r2/21-069r2.html)).
- **Validate every JSON example against the relevant JSON Schema** before trusting
  it (the CovJSON guide's examples were all validated against the OGC CoverageJSON
  schema; keep that discipline for future format guides).

## Callouts

Quarto `::: {.callout-note|tip|important|warning|caution}`. Use **sparingly**,
especially `note` -- too many dilute the signal. Reserve `warning`/`caution` for
genuine traps and `important` for the single central idea of a section.

## Diagrams (a priority)

Two lanes, documented in `diagrams/README.md`:

1. **Hand-authored "hero" SVGs** for teaching core concepts -- commit the `.svg`.
   Palette: domain/inputs indigo `#6366f1`, range/outputs teal `#14b8a6`, neutral
   slate `#0f172a`. First one: `diagrams/coverage-as-function.svg`.
2. **Text-based** Mermaid / Graphviz / D2 for structural diagrams (Quarto renders
   to SVG at build; diffs cleanly).

Reuse the guides' vocabulary verbatim in labels so prose and picture match. The
full, matured conventions (B-bar, color/accessibility, motion, captions) now live
in `diagrams/README.md`. The CovJSON guide's main ASCII diagrams (the row-major
grid; the parameter <-> range key binding) have been converted; convert further
ASCII blocks as the series grows.

## Chuck's workflow preferences

- **Chuck commits himself**: stage changes, do NOT auto-commit.
- **SHA-pin** all GitHub Actions (and pre-commit revs) to commit SHAs, with a tag
  comment.
- Install tools via **Homebrew + `~/.Brewfile`**; never `curl | bash` installers.
- Collaborative authoring: draft a section, then **probe Chuck's understanding**
  as the representative learner before moving on. He likes being asked.

## Status

- Scaffold complete and **building cleanly** (all pages render, no errors).
- `formats/coveragejson.qmd` is the first complete guide, **ported from**
  `developmentseed/titiler-covjson/docs/00-coveragejson-beginners-guide.md`.
  It is the template for the series.
- **Canonical home resolved:** this series is the home for the CovJSON guide. The
  original draft in `developmentseed/titiler-covjson` was removed (2026-06-19) and
  that repo returned to its prior state, so there is no duplicate to drift.
- **Tooling wired up (2026-06-19):** CI, Pages publish, Dependabot, pre-commit,
  and hygiene files added (see Tech & build). Repo is still **local-only** -- no
  GitHub remote yet, so the workflows haven't run; deploy needs Pages set to the
  "GitHub Actions" source once a remote exists.
- **Initial commit landed 2026-06-22** (scaffold + CovJSON guide + tooling +
  diagram design docs).
- Remaining content (foundations spine, `zarr.qmd`) is stubs.

### CoverageJSON diagram pass -- DONE (2026-06-22)

Executed inline. Conventions are codified in `diagrams/README.md`; the as-built
result and the deviations from the plan are in the "Outcome" note of
`docs/superpowers/plans/2026-06-22-covjson-diagrams.md`.

- Diagrams now in the CovJSON guide: `coverage-as-function.svg` (enhanced with an
  amber worked instance), `coverage-shared-domain.svg` (one domain -> multiple
  ranges -- **replaced** the planned spreadsheet hero), `parameter-range-binding.svg`
  (Section 3.3), `row-major-grid.svg` (animated, Section 4.3).
- Also: site-wide figure caption styling (`custom.scss` + `caption-labels.html`);
  a "Property, parameter, range" callout in 3.3; reworded 2.1 coverage definition
  ("one function per property, all sharing the same domain"); spreadsheet mental
  model removed from the prose.
- Established the **B-bar / animated-C** pattern and the matured palette,
  accessibility, motion, and caption conventions for the series.

## Prior art (build on, don't duplicate)

- **Project Pythia Foundations** (Jupyter Book; geoscience Python stack) -- a model
  and a possible upstream contribution target for the Xarray guide.
- **Cloud-Native Geospatial Foundation** formats guide (COG/Zarr/STAC/GeoParquet).

## Parked side-quest

A plan to join Zarr/GeoZarr coordinate-convention conversations to advocate
CoverageJSON is **parked until Chuck is more CovJSON-fluent**. Reminder cue:
"revisit the Zarr/CoverageJSON conversations." Full details are in this project's
Claude memory (`covjson-geozarr-engagement`), carried over from the
titiler-covjson work.

## Backlog / next steps

- Write `foundations/geospatial-concepts.qmd` (the spine the others hang from).
  Brainstormed shape: a short **spine page + 4 sub-pages** (Where things are /
  Raster vs. vector / Coverages / Footprints & point data); spine is a
  mental-model overview that links out ("a pixel's journey" framing held in
  reserve). First round: spine page only, sub-pages as stubs.
- Create the GitHub remote, then enable Pages (source: **GitHub Actions**) so
  `publish.yml` can deploy.
- Decide which library guides to include, then restore the `libraries/` section.
- Convert more ASCII diagrams to SVG; grow the diagram style vocabulary.
