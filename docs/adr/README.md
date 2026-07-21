# Architecture Decision Records

This directory holds Architecture Decision Records (ADRs): the append-only
record of cross-cutting design decisions whose rationale a reader could not
recover from the rendered site alone. This is a "write to learn, publish the
result" series, and the ADRs are where the *why* behind a decision survives
(the visual system, the tooling, the guide-wide conventions). CLAUDE.md carries
the standing working agreements; this README covers only the mechanics of the
directory.

These files are never published as pages: `_quarto.yml` restricts rendering to
`*.qmd`, so the ADRs are repo-internal and never appear on the site.

## Numbering

ADRs are numbered with a zero-padded, plain-sequential `NNNN` prefix
(`0001-title.md`, `0002-title.md`, ...), assigned in the order they are
accepted, regardless of topic. The number is permanent: a superseded ADR keeps
its file and number and is marked `Superseded by ADR-NNNN` rather than deleted
or renumbered.

`template.md` is the copyable starting point and is intentionally not numbered,
so it never consumes a sequence number.

## What "append-only" protects

The decision and its rationale, not the vocabulary. An accepted ADR is never
renumbered or deleted, and a decision that is later reversed is superseded
rather than rewritten, so the record of what was chosen (and why) survives even
once it stops being true.

Everything else is maintenance. An ADR is amended in place to sweep a rename so
it still resolves to names that exist, or to sharpen its reasoning as a decision
proves itself. The test: does the edit change what the ADR decided, or why? If
not, it is maintenance; make it. If so, supersede instead.

## Format

Each ADR follows the lightweight template in [template.md](template.md):

- **Title**: `# ADR-NNNN: <decision>`.
- **Status**: `Accepted` or `Superseded by ADR-NNNN`.
- **Context**: the forces at play; what made this a decision worth recording.
- **Decision**: what we chose, stated plainly.
- **Alternatives considered**: the real rejected options and why they lost.
- **Consequences**: what follows, including the costs we accept.

Keep each ADR self-contained, and do not restate conventions already in
CLAUDE.md. A decision's supporting material (a design mockup, a reference
artifact) lives under [assets/](assets/) and is linked from its ADR.

## Index

- [ADR-0001](0001-record-design-decisions-as-adrs.md): record cross-cutting
  design decisions as ADRs, retiring the tool-specific `docs/superpowers/`
  specs-and-plans layout (resolves #17).
- [ADR-0002](0002-visual-redesign-survey-console.md): the visual redesign, its
  "Survey Console" direction, the "Terra Rusty" palette (green, over the blue
  "Tidewater" twin), and self-hosted OFL fonts only (issue #6).
- [ADR-0003](0003-teaching-diagrams-theme-adaptive.md): re-author all 15
  teaching diagrams as theme-adaptive, token-driven inline SVGs in one pass,
  absorbing the #12 mobile-legibility fix (issue #6).
