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
projections, raster vs. vector, coverages, footprints, COG, STAC).

Personal ramp-up project by Chuck Daniels (chuck@developmentseed.org). Dev Seed
does heavy NASA/EO work and maintains the rio-tiler / TiTiler stack. May later
move to the `developmentseed` org; for now it lives under `~/src/chuckwondo`.

### A shelf, not a curriculum (decided 2026-07-16)

The series is a **publishing brand plus shared conventions**, not a curriculum
with a dependency spine. This is load-bearing; do not quietly re-litigate it.

- **Every guide is standalone and self-foundational**, with a real title of its
  own ("CoverageJSON Uncovered", "COG Uncovered"). The series is the shelf, not
  the book. Readers arrive from a search for their actual problem and will not
  read a prerequisite page first.
- **The site publishes what is done; the roadmap lives here.** No "planned" pages,
  no stub sections. A page advertising unwritten work makes a finished artifact
  look unfinished, which is a real cost paid today for a speculative benefit.
- **A guide absorbs the slice of foundations it needs.** The CovJSON guide already
  did this in its Section 2 and works standalone. Foundations are less shared than
  a spine implies: CovJSON needs `referencing` and axis semantics, COG needs
  projections and tiling schemes. Extract shared material only once the overlap
  actually hurts.
- **One repo.** N repos means N CI configs, N licenses, and diagram conventions
  that stay in sync only by discipline. Splitting would also undo the deliberate
  2026-06-19 consolidation of the CovJSON guide into this one repo.
- Foundations is not cancelled, it is **promoted**: "Where Things Are: CRS &
  Projections for Engineers" becomes a standalone peer guide, not homework.

Corollary: pick the next guide by **largest knowledge gap x closest to the day
job**, not by what would make the series feel complete.

## Audience & voice

- Reader: seasoned software engineer, new to geospatial/EO. Trust their code
  fluency; teach the domain.
- **Mind the middle band.** "Trust code fluency, teach the domain" splits the
  world in two, but a third category sits between: adjacent tech that is neither
  geospatial nor universal. Parquet, TIFF/IFD internals, HDF5, columnar storage,
  protobuf. A strong backend engineer may never have touched any of them.
  - JSON, HTTP, CLIs, data structures: safe to assume.
  - Geospatial: teach it.
  - Adjacent-but-not-universal: **ask, do not assume.**
  - **Chuck is the calibration instrument** (he is the representative learner):
    if Chuck does not know it, the reader baseline must not assume it. Before
    scoping a guide, name what it assumes and check the middle-band items with
    him explicitly.
  - The check often **narrows** a guide rather than growing it. "The reader does
    not know compression" does not mean adding a DEFLATE primer to the COG guide;
    it means noticing that COG only ever needed one property ("tiles decode
    independently"), and the rest was scope creep wearing a prerequisite's
    clothes.
- Patient, example-driven, **diagram-forward** (engaging visuals are a core goal,
  not an afterthought).
- Set expectations honestly: these are practical on-ramps, not exhaustive
  treatments; point to the specs for depth.

## Tech & build

- Engine: **Quarto** (website project). Quarto 1.9.38 is installed (via
  `brew install --cask quarto`).
- `quarto preview` for live local view; `quarto render` builds into `_site/`.
- `_quarto.yml`: docked sidebar (a flat peer list: Home + each guide), light
  (`cosmo`) + dark (`darkly`) themes, search. `render` is restricted to `*.qmd`
  so `CLAUDE.md`/`README.md` are not turned into pages. The pedagogical section
  headers were removed by #9; grouping can return if the guide count ever
  justifies it.
- **No Python environment yet.** Every page is currently prose and renders with
  no kernel, so `environment.yml` was removed (YAGNI). When the first
  *executable* guide gets real code, add the env then -- likely **uv**
  (`pyproject.toml` + `uv.lock`): the planned libs (xarray, rio-tiler, zarr,
  virtualizarr, etc.) are all pip-installable, and uv gives a lockfile,
  lockfile-pinned CI, and Dependabot-tracked deps (conda has none of these).
  - The COG guide (#14) is the likely trigger: its correctness harness is
    `rio cogeo validate` against real files, which means real deps. Make the
    call there.
  - Schema validation in CI (#10) does **not** trigger it: `uvx jsonschema`
    needs no project environment. Keep it that way.
- **CI/CD & hygiene** (`.github/` + repo root): `ci.yml` renders the site then
  runs an offline lychee link-check; `publish.yml` deploys to GitHub Pages via
  OIDC (no `gh-pages` branch, no secret), serving the live site at
  <https://chuckwondo.github.io/geospatial-for-engineers/>; `dependabot.yml` does
  weekly github-actions bumps; `.pre-commit-config.yaml`
  (+ `scripts/pin-precommit-shas.sh`), `.editorconfig`, `.gitattributes` cover
  local hygiene. All Actions and pre-commit revs are SHA-pinned with tag
  comments; run `prek run --all-files`.
  - **`prek install` is required, per clone, and nothing enforces it.** It was
    never run here until 2026-07-16, so every hook was inert for the repo's
    first month: they only fired when someone typed `prek run --all-files` by
    hand. **CI does not run prek either**, so a missed `prek install` has no
    backstop. If hygiene ever looks suspiciously clean, check
    `ls .git/hooks/` first.
  - `default_install_hook_types` names both `pre-commit` and `commit-msg`, so
    one bare `prek install` sets up both shims. Without it, `prek install`
    installs only `pre-commit` and silently leaves gitlint inert -- the same
    class of failure as above, one level down.

## Layout

As-built today. Every page here is finished work (#9 landed).

- `index.qmd` -- landing page + the framing.
- `guides/` -- one file per guide. `guides/coveragejson.qmd` is the only one so
  far.
- `diagrams/` -- concept diagrams + their conventions (`diagrams/README.md`).

### Taxonomy: settled 2026-07-16 (#9)

Guides live in `guides/`, flat. `formats/`, `foundations/`, and `how-to/` are
gone.

`guides/` **classifies nothing, and that is the point.** Under the shelf model
every page is a guide, so the directory carries no information and therefore
cannot become wrong. `formats/` classified, and would have broken the moment a
non-format landed.

Do not re-litigate this by adding a category directory. If the guide count ever
makes navigation hard, that is a sidebar problem (#6), not a filesystem one.

Two things worth knowing, because both were nearly missed:

- **#9 originally deferred this to #14**, reasoning that "the second guide forces
  the taxonomy question (CRS is not a format)." That trigger would never have
  fired: #14 is COG, and COG **is** a format, so it would have slotted into
  `formats/` cleanly and punted to guide #3, by which point there would be more
  inbound links to break. Beware forcing functions aimed at the wrong guide.
- **Moving is cheapest when a PR is already rewriting the inbound links.** #9
  was; a standalone move PR would not be.

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
  schema; keep that discipline for future format guides). Still **manual** until
  #10 automates it. The authority is `https://schemas.opengis.net/covjson/1.0/`
  (the spec's Conformance clause), **not** `covjson.org/schema/dev`. Most examples
  are fragments needing a Domain wrapper first, and the schema checks types, not
  sense: green is necessary, not sufficient.

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
grid; the parameter <-> range key binding) have been converted.

**"Convert the remaining ASCII blocks" is the wrong goal**; converting the ones
that are *diagrams* is the right one (#13). A block that is code-shaped (the
Coverage skeleton in §3.1: a JSON object with inline annotations) stays text: the
reader parses JSON natively, and an SVG would make it a picture of something they
would rather read, and kill copy-paste. Convert a block when the picture can
carry meaning the text physically cannot (e.g., color for the land-cover
categories in §5.5).

## Chuck's workflow preferences

- **Chuck commits himself**: stage changes, do NOT auto-commit. Drafting the
  commit message is welcome; running `git commit` is not.
- **Commit messages**: conventional-commit subject (`docs:`, `feat:`, `chore:`,
  optional scope), and **wrap at 72** -- subject and body alike. Enforced at
  commit time by gitlint (`.gitlint`) via the commit-msg hook, so a bad message
  is rejected rather than merely noticed; `--no-verify` bypasses it. Merge
  commits are exempt (gitlint ignores them), so GitHub's "Merge pull request
  #NN from ..." subjects are fine. History before 2026-07-16 drifted to 75 and
  80 columns; do not copy it.
- **SHA-pin** all GitHub Actions (and pre-commit revs) to commit SHAs, with a tag
  comment.
- Install tools via **Homebrew + `~/.Brewfile`**; never `curl | bash` installers.
- Collaborative authoring: draft a section, then **probe Chuck's understanding**
  as the representative learner before moving on. He likes being asked.

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

## Candidate guides (not yet filed)

Ranked by the gap x day-job rule. Chuck self-reported gaps in **all** of these as
of 2026-07-16, so the gap axis does not discriminate; rank on day-job proximity
and fit with the framing.

- **STAC Uncovered** -- Dev Seed core, JSON the reader can read cold. The
  underserved part is the *why*: why a tree of static JSON instead of a database.
- **Where Things Are: CRS & Projections** -- the deepest, most-searched
  confusion, and the most-covered. Only wins if the engineer reframing carries
  it: lon/lat order as an API contract bug, EPSG as a registry lookup, datums as
  a versioning problem. Done conventionally it is redundant.
- **Tiling schemes / TileMatrixSet** -- why Web Mercator won despite lying about
  area. Pairs with COG.
- **GeoParquet** -- **do not assume the reader knows Parquet** (Chuck does not;
  see the middle band). That makes it ~1.5 guides, not the cheap "geo delta" win
  it first looks like, and it drifts toward data engineering.
- **Footprints, swaths & point data (GEDI)** -- genuinely underserved; was in the
  original Foundations sketch.
- The three unfiled CovJSON how-tos sketched in the old `how-to/index.qmd` stub
  (deleted by #9, recorded here so they are not lost): external references & lazy
  loading, non-standard domains & custom axes, and `parameterGroups` for vector
  quantities.
- **Zarr: still off the list, but not for the reason previously recorded.** The
  old note said [developmentseed/zarr-book](https://developmentseed.org/zarr-book/)
  "fills it, and better." **That did not survive checking.** As of 2026-07-16 the
  zarr-book is itself stubs: three commits total, the last "initial stubs for the
  zarr-book" (2026-06-01) and nothing since; every chapter under 3.3KB; and
  `data-model/chunks.md` opens with "**Status:** stub" under a
  "What this chapter will cover" heading.
  - So **do not link readers to it** until it has real content, and do not treat
    the Zarr gap as filled. #9 links `zarr.dev` instead.
  - Zarr stays off the candidate list on **day-job-proximity** grounds (the rule
    is gap x day-job), and because it is Dev Seed's own book and the natural home
    if it revives. Revisit if it is still idle.
  - The connective tissue (how Zarr relates to the coverage model) remains what
    this series would be uniquely placed to add.
  - General lesson: this premise sat in `CLAUDE.md` unchecked and nearly shipped
    reader-facing links to someone else's stubs. Verify "X covers it better"
    claims before building on them.
