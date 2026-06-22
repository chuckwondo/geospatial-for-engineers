# Geospatial for Engineers

A series of beginner's guides to geospatial and earth-observation (EO) data,
written for **experienced software engineers with zero domain background**.

Most geospatial tutorials assume the domain knowledge (CRS, projections,
coverages, footprints, COG, Zarr) and explain the code. This series does the
opposite: it assumes you can already code and spends its words on the domain.

Built with [Quarto](https://quarto.org/) and published as a static website.

## Contents

- **Foundations** -- core geospatial concepts (the spine of the series).
- **Data formats** -- CoverageJSON, Zarr, and more.
- **Libraries & tools** -- planned, once the foundations and formats are in place.

## Building locally

Requires Quarto (`brew install --cask quarto`).

```bash
quarto preview      # live-reloading local preview
quarto render       # build the static site into _site/
```

## Visualizations

Concept diagrams live in `diagrams/`. See `diagrams/README.md` for the
authoring conventions (hand-authored SVG for "hero" concept graphics; text-based
Mermaid/Graphviz/D2 for structural diagrams).

## Status

Work in progress. The CoverageJSON guide is the first complete entry and the
template for the rest; other guides are stubs.

## Continuous integration

`.github/workflows/ci.yml` renders the site and runs an offline link check on
every push and pull request. Local checks are configured in
`.pre-commit-config.yaml` (file hygiene, spell-check, workflow lint/security);
install them with `prek install` (or `pre-commit install`) and run with
`prek run --all-files`. All GitHub Actions and pre-commit hooks are pinned to
commit SHAs; refresh them with `prek autoupdate && scripts/pin-precommit-shas.sh`.

## Publishing

`.github/workflows/publish.yml` renders the site and deploys it to GitHub Pages
on every push to `main`, using `quarto-dev/quarto-actions` and the official
Pages deployment (OIDC -- no `gh-pages` branch, no stored secret). It activates
once the repo has a GitHub remote and **Pages is enabled with "GitHub Actions"
as the source** (Settings -> Pages).
