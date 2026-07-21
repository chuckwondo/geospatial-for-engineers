# ADR-0001: Record design decisions as ADRs

## Status

Accepted

## Context

This is a "write to learn, publish the result" series: guides are authored from
partial knowledge, and the disciplines that keep that honest (validating every
example, citing specs precisely, and recording *why* a cross-cutting choice was
made) are the point, not hygiene. The design decisions behind the site (the
visual system, the tooling, guide-wide conventions) were being recorded as dated
specs and plans under `docs/superpowers/specs/` and `docs/superpowers/plans/`, a
layout carried over from the Claude Code authoring workflow that produced them.

That layout has two problems. Its name and its specs-versus-plans split are
specific to one authoring tool, and this is a public repository, so the
vocabulary of a particular tool leaks into the record for no reader's benefit.
More fundamentally, a spec and a plan are the wrong genre for a durable record:
a spec describes a whole design and a plan is an execution checklist, and both go
stale the moment the work ships and the code becomes the truth. The thing worth
keeping is narrower and more durable: the decision, and the reasoning and
rejected alternatives behind it.

Issue #17 asked whether the `docs/superpowers/` material should be tracked at
all, and noted that the convention had no recorded rationale, so a fresh
contributor could not tell whether to add to it. This ADR answers that.

## Decision

Record cross-cutting design decisions as Architecture Decision Records under
`docs/adr/`, in the lightweight Nygard format (Status, Context, Decision,
Alternatives considered, Consequences), numbered sequentially. Retire
`docs/superpowers/`.

The specs and the plan previously under `docs/superpowers/` describe work that
has already shipped, and their conventions already live where they belong (the
diagram conventions in `diagrams/README.md`, the guide structure in the guide
itself). They are not retrofitted into ADRs; git history preserves them. ADRs
start fresh from the decisions that are still live.

## Alternatives considered

- **Keep `docs/superpowers/specs` and `plans` as the tracked record.** Rejected
  on both counts above: the naming is tool-specific in a public repo, and the
  spec-and-plan genre goes stale against shipped work, whereas the decision and
  its rationale do not.
- **Stop tracking design rationale entirely, and rely on git history and the
  issue tracker.** Rejected. This series is written to learn in public; the *why*
  behind a cross-cutting choice is exactly what a reader cannot recover from the
  rendered site or a diff, and a closed issue's thread is not a first-class,
  discoverable record the way a numbered ADR is.
- **Back-date the shipped specs into ADRs for a complete record from the
  project's start.** Rejected. Those decisions are executed and their conventions
  are already codified elsewhere, so writing ADRs for them now would mean
  inventing an "Alternatives considered" section for choices that were never
  genuinely weighed as competing options. That is padding, not record.

## Consequences

- `docs/adr/` is the home for cross-cutting design decisions. `_quarto.yml`
  restricts rendering to `*.qmd`, so ADRs are repo-internal and never appear as
  published pages.
- A new cross-cutting decision earns an ADR. A standing convention belongs in
  CLAUDE.md and is not restated in an ADR; the two are complementary, and the ADR
  README states the split.
- Supporting material for a decision (a design mockup, a reference artifact)
  lives under `docs/adr/assets/` and is linked from its ADR, so an ADR can point
  to an exact realization without inlining it.
- Resolves #17.
