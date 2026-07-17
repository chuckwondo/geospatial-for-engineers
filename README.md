# Geospatial for Engineers

A series of beginner's guides to geospatial and earth-observation (EO) data,
written for **experienced software engineers with zero domain background**.

Most geospatial tutorials assume the domain knowledge (CRS, projections,
coverages, footprints, COG, Zarr) and explain the code. This series does the
opposite: it assumes you can already code and spends its words on the domain.

Built with [Quarto](https://quarto.org/) and published as a static website.

## The guides

Each guide is standalone and self-foundational: it teaches the domain concepts
it needs, assumes you can already code, and is meant to be read on its own. The
series is the shelf, not a book with a required reading order. The site
publishes each guide as it is finished.

- **[CoverageJSON Uncovered](guides/coveragejson.qmd)**: the CoverageJSON
  format for multidimensional coverage data, explained for engineers.

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

Live at <https://chuckwondo.github.io/geospatial-for-engineers/>. The
CoverageJSON guide is the first complete entry and the template for the series;
further guides are added as they are written.

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
Pages deployment (OIDC: no `gh-pages` branch, no stored secret). It activates
once the repo has a GitHub remote and **Pages is enabled with "GitHub Actions"
as the source** (Settings -> Pages).

## License

This project is dual-licensed to match what it contains:

- **Prose and diagrams** (the guides, the SVG and text-based diagrams, and the
  site content) are licensed under
  [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/).
  Reuse, adapt, and translate freely, including commercially; just give credit.
  Full text in [`LICENSE`](LICENSE).
- **Code** (`_quarto.yml`, `custom.scss`, `caption-labels.html`, everything
  under `scripts/`, the GitHub Actions workflows, and any future source) is
  licensed under the [MIT License](https://opensource.org/license/mit). Full
  text in [`LICENSE-CODE`](LICENSE-CODE).

Copyright (c) 2026 Charles "Chuck" Daniels. Suggested attribution: "Chuck
Daniels, Geospatial for Engineers."
