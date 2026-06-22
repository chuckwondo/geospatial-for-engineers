# CoverageJSON Guide Diagram Pass Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add three polished diagrams to the CoverageJSON guide and codify the matured visual conventions, establishing the reusable "diagram-forward" pattern for the series.

**Architecture:** Hand-coded SVG committed as source. Every diagram meets a static "B" quality bar (palette, light card, explanatory device, accessibility metadata); a "C" motion layer (CSS `@keyframes` inside the SVG, with a `prefers-reduced-motion` guard) is added only where motion teaches what a static frame cannot. Figures are embedded in `formats/coveragejson.qmd` via Quarto image syntax, replacing the ASCII blocks they supersede.

**Tech Stack:** Quarto 1.9.x (website project), hand-authored SVG with inline CSS, `xmllint` (well-formedness), `quarto render`/`quarto preview` (build + visual verification), Chrome DevTools "Emulate vision deficiencies" + "prefers-reduced-motion" (accessibility verification).

## Outcome (completed 2026-06-22)

All tasks executed. Two changes were decided collaboratively during execution and
diverge from the plan as written:

1. **Caption styling was added** (not in the original plan): site-wide figure/table
   caption styling via `custom.scss` + `caption-labels.html` -- smaller, muted, a
   constrained `min(40rem, 90%)` measure, and a bold "Figure N:" label (a load-time
   JS shim wraps the locator, which Quarto emits as a bare text node). Wired into
   both themes in `_quarto.yml`. Committed separately.
2. **Task 5 was redesigned.** The `coverage-as-spreadsheet.svg` hero was dropped --
   the spreadsheet metaphor was judged a poor fit (implies a rigid grid,
   skeuomorphic, redundant with the function hero, visually busy). It was replaced
   with a **function pair**: `coverage-as-function.svg` enhanced with an amber
   worked instance + equal-count parallel domain/range grids, plus a new
   `coverage-shared-domain.svg` (one shared domain -> multiple ranges, the plural
   `ranges` idea). The Section 2.1 callout was reworded to "one function per
   property, all sharing the same domain"; the spreadsheet mental model was removed
   from the prose (2.1 + the 5.3 back-reference); and a "Property, parameter, range"
   callout was added to 3.3. `#fig-shared-domain` replaces the planned
   `#fig-coverage-spreadsheet`; there is no `coverage-as-spreadsheet.svg`.

Matured conventions are codified in `diagrams/README.md` (Task 6).

## Global Constraints

- **Chuck commits himself.** Never run `git commit`. Each task ends by staging (`git add`) the relevant files and pausing for Chuck to commit.
- **Palette:** domain/inputs indigo `#6366f1`; range/outputs teal `#14b8a6`; neutral slate `#0f172a`; links slate-700 `#334155`; amber `#f59e0b` for highlight/motion ONLY (never a category color).
- **Contrast rule:** pastel tints for fills (`#eef2ff`, `#f0fdfa`, `#e0e7ff`, `#ccfbf1`); darker indigo/teal for strokes, borders, and small text (`#4338ca` indigo-700, `#0f766e` teal-700). Never use light teal `#14b8a6` as a stroke (only ~2.5:1 on white).
- **No color-alone encoding:** every categorical distinction is also carried by position, a text label, or a bracket/shape.
- **Accessibility:** every SVG has `role="img"` + linked `<title>` and `<desc>`; labels reuse the guide's prose vocabulary verbatim.
- **Light card:** white (`#fff`) rounded rect, hairline border `#e2e8f0`, so diagrams read on both `cosmo` (light) and `darkly` (dark) themes.
- **Spec:** `docs/superpowers/specs/2026-06-22-covjson-diagrams-design.md`.

---

### Task 1: Animation feasibility spike (retire the one technical risk)

Confirm that CSS `@keyframes` inside an SVG animate when the SVG is embedded through Quarto's Markdown `![](...)` (`<img>`) mechanism, and that `prefers-reduced-motion` freezes it. This decides the embedding approach for Task 4 before any real motion work.

**Files:**
- Create (scratch, deleted at end): `diagrams/_spike-anim.svg`, `_spike-anim.qmd`

**Interfaces:**
- Produces: a verified embedding decision — either "CSS-in-SVG animates via `![](...)`" (preferred) or "use a raw inline-SVG HTML block for the animated diagram." Task 4 consumes this decision.

- [ ] **Step 1: Create a minimal animated test SVG**

Create `diagrams/_spike-anim.svg`:

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 200 80" role="img" aria-labelledby="sp-t">
  <title id="sp-t">Animation spike</title>
  <style>
    .dot { animation: sp-move 2s ease-in-out infinite alternate; }
    @keyframes sp-move { from { transform: translateX(0); } to { transform: translateX(120px); } }
    @media (prefers-reduced-motion: reduce) { .dot { animation: none; } }
  </style>
  <rect x="1" y="1" width="198" height="78" rx="10" fill="#ffffff" stroke="#e2e8f0" stroke-width="2"/>
  <circle class="dot" cx="40" cy="40" r="12" fill="#f59e0b"/>
</svg>
```

- [ ] **Step 2: Embed it in a scratch Quarto page**

Create `_spike-anim.qmd`:

```markdown
---
title: "Animation spike"
---

![Animation spike test](diagrams/_spike-anim.svg){#fig-spike}
```

- [ ] **Step 3: Verify the SVG is well-formed**

Run: `xmllint --noout diagrams/_spike-anim.svg`
Expected: no output (exit 0). Any error means malformed XML — fix before continuing.

- [ ] **Step 4: Render and view in the browser**

Run: `quarto preview --port 4848 --no-browser` (or reuse the running preview).
Open `http://localhost:4848/_spike-anim.html`.
Expected: the amber dot slides left-to-right and back, proving CSS-in-SVG animates through `<img>`.

- [ ] **Step 5: Verify the reduced-motion fallback**

In Chrome DevTools: Rendering panel -> "Emulate CSS media feature prefers-reduced-motion" -> `reduce`. Reload.
Expected: the dot is static (no motion).

- [ ] **Step 6: Record the decision and clean up**

If the dot animated in Step 4: the embedding decision is **`![](...)` works** — Task 4 uses normal image embedding.
If it did NOT animate (some browsers gate `<img>`-SVG animation): the decision is **use a raw HTML inline-SVG block** in the qmd for the animated diagram:

````markdown
```{=html}
<figure>… inline <svg>…</svg> …</figure>
```
````

Delete the scratch files:

Run: `rm diagrams/_spike-anim.svg _spike-anim.qmd`

- [ ] **Step 7: Stage (Chuck commits)**

There is nothing to commit (scratch files deleted). Note the decision in the chat for Task 4. No staging needed.

---

### Task 2: Fix the teal-stroke contrast in the existing hero SVG

`diagrams/coverage-as-function.svg` uses light teal `#14b8a6` as the RANGE card stroke (~2.5:1 on white, below the 3:1 non-text-contrast bar). Switch the stroke to teal-700 `#0f766e`; keep the light teal *fill*.

**Files:**
- Modify: `diagrams/coverage-as-function.svg`

**Interfaces:**
- Produces: nothing consumed by later tasks (independent quality fix).

- [ ] **Step 1: Verify the current offending stroke is present**

Run: `grep -n 'stroke="#14b8a6"' diagrams/coverage-as-function.svg`
Expected: one match, on the RANGE `<rect>` (the card at `x="462"`).

- [ ] **Step 2: Change the RANGE card stroke to teal-700**

In `diagrams/coverage-as-function.svg`, on the RANGE card rect:

```
<rect x="462" y="96" width="250" height="156" rx="14" fill="#f0fdfa" stroke="#0f766e" stroke-width="2.5"/>
```

(Only the `stroke` value changes, from `#14b8a6` to `#0f766e`. Leave the indigo DOMAIN card and all fills unchanged.)

- [ ] **Step 3: Verify no light-teal stroke remains and the file is well-formed**

Run: `grep -c 'stroke="#14b8a6"' diagrams/coverage-as-function.svg; xmllint --noout diagrams/coverage-as-function.svg`
Expected: `0` (no light-teal strokes left), and no xmllint output.

- [ ] **Step 4: Visually confirm in both themes**

In `quarto preview`, open the CoverageJSON guide, find @fig-coverage-function. Toggle the light/dark theme switch.
Expected: the teal card border is now clearly visible (darker) and the figure reads on both themes; the fill is unchanged.

- [ ] **Step 5: Stage (Chuck commits)**

Run: `git add diagrams/coverage-as-function.svg`
Then pause for Chuck to commit. Suggested message: `fix(diagrams): use teal-700 stroke on coverage-as-function range card for contrast`

---

### Task 3: `parameter-range-binding.svg` (static B) + embed in Section 3.3

Replace the Section 3.3 ASCII key-binding block with a static SVG: two side-by-side cards (`parameters`, `ranges`) whose shared key `"temperature"` is highlighted in both, joined by a connector; a faint `"humidity"` pair hints how a second property adds a second matching key.

**Files:**
- Create: `diagrams/parameter-range-binding.svg`
- Modify: `formats/coveragejson.qmd` (Section 3.3, replace the ASCII block at the lines containing the `┌─▶` / `└─▶` diagram)

**Interfaces:**
- Consumes: the B-bar conventions (Global Constraints).
- Produces: the established static-diagram + embedding workflow that Tasks 4-5 follow.

- [ ] **Step 1: Author the SVG**

Create `diagrams/parameter-range-binding.svg`:

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 520 250" role="img" aria-labelledby="pb-title pb-desc" font-family="-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif">
  <title id="pb-title">A shared key binds a parameter to its range</title>
  <desc id="pb-desc">Two cards side by side: a parameters card on the left and a ranges card on the right. Both contain a key named temperature, highlighted and joined by a connector, showing that the matching key binds the description of a property to its values. A faint humidity entry in each card shows that a second property adds a second matching key.</desc>

  <rect x="1" y="1" width="518" height="248" rx="16" fill="#ffffff" stroke="#e2e8f0" stroke-width="2"/>

  <!-- parameters card (the "what") -->
  <rect x="34" y="62" width="190" height="150" rx="12" fill="#f8fafc" stroke="#475569" stroke-width="2"/>
  <text x="48" y="86" font-size="14" font-weight="700" fill="#0f172a" font-family="ui-monospace, Menlo, monospace">"parameters"</text>
  <text x="48" y="104" font-size="11" fill="#64748b">what each property is</text>

  <!-- ranges card (the values) -->
  <rect x="296" y="62" width="190" height="150" rx="12" fill="#f0fdfa" stroke="#0f766e" stroke-width="2"/>
  <text x="310" y="86" font-size="14" font-weight="700" fill="#0f766e" font-family="ui-monospace, Menlo, monospace">"ranges"</text>
  <text x="310" y="104" font-size="11" fill="#0d9488">the values</text>

  <!-- shared key highlight: temperature (amber) -->
  <rect x="44" y="120" width="150" height="26" rx="6" fill="#fef3c7" stroke="#f59e0b" stroke-width="1.5"/>
  <text x="52" y="138" font-size="13" font-weight="700" fill="#92400e" font-family="ui-monospace, Menlo, monospace">"temperature":</text>
  <rect x="306" y="120" width="170" height="26" rx="6" fill="#fef3c7" stroke="#f59e0b" stroke-width="1.5"/>
  <text x="314" y="138" font-size="13" font-weight="700" fill="#92400e" font-family="ui-monospace, Menlo, monospace">"temperature":</text>

  <!-- connector for the shared key -->
  <line x1="194" y1="133" x2="306" y2="133" stroke="#f59e0b" stroke-width="2.5"/>
  <text x="250" y="124" text-anchor="middle" font-size="10" font-weight="600" fill="#b45309">shared key</text>

  <!-- faint second pair: humidity -->
  <text x="52" y="180" font-size="13" fill="#94a3b8" font-family="ui-monospace, Menlo, monospace">"humidity":</text>
  <text x="314" y="180" font-size="13" fill="#94a3b8" font-family="ui-monospace, Menlo, monospace">"humidity":</text>
  <line x1="194" y1="176" x2="306" y2="176" stroke="#cbd5e1" stroke-width="1.5" stroke-dasharray="3 3"/>

  <text x="260" y="232" text-anchor="middle" font-size="11" fill="#64748b">the matching key binds the description to its values</text>
</svg>
```

- [ ] **Step 2: Verify well-formedness and the contrast/accessibility invariants**

Run:
```
xmllint --noout diagrams/parameter-range-binding.svg
grep -c 'stroke="#14b8a6"' diagrams/parameter-range-binding.svg
grep -c '<title' diagrams/parameter-range-binding.svg
grep -c '<desc' diagrams/parameter-range-binding.svg
```
Expected: no xmllint output; `0` light-teal strokes; `1` title; `1` desc.

- [ ] **Step 3: Replace the ASCII block in Section 3.3**

In `formats/coveragejson.qmd`, find the fenced ```` ```text ```` block in Section 3.3 that draws the `┌─▶ "temperature"` binding (currently between the "Lining the two objects up side by side makes the binding obvious:" sentence and "The parameter says what the numbers mean..."). Delete the entire fenced block and replace it with:

```markdown
![The shared key (here `temperature`) is the glue: a parameter and the range with the same key describe the same property. A second property simply adds a second matching key in each object.](../diagrams/parameter-range-binding.svg){#fig-parameter-range-binding}
```

- [ ] **Step 4: Render and verify the figure appears, on both themes**

Run: `quarto render formats/coveragejson.qmd`
Then in `quarto preview` open the guide at Section 3.3.
Expected: the figure renders with its caption in place of the old ASCII; toggling light/dark keeps it readable; the amber-highlighted shared key and connector are clear.

- [ ] **Step 5: Accessibility verification (grayscale + CVD)**

In Chrome DevTools Rendering panel: set "Emulate vision deficiencies" to `Achromatopsia` (grayscale), then `Deuteranopia`.
Expected: the binding is still understandable in every mode — the cards are distinguished by their `"parameters"`/`"ranges"` labels and position, and the shared key by the connector and the highlight box outline, not by color alone.

- [ ] **Step 6: Stage (Chuck commits)**

Run: `git add diagrams/parameter-range-binding.svg formats/coveragejson.qmd`
Then pause for Chuck to commit. Suggested message: `feat(diagrams): add parameter-range key-binding figure to CovJSON 3.3`

---

### Task 4: `row-major-grid.svg` (B + C motion) + embed in Section 4.3

Replace the Section 4.3 ASCII fold block with a diagram: the flat `values` list `[1..6]`, bracketed into its two rows and color-mapped onto the 2x3 grid, with an "x varies fastest" cue (B), plus a looping amber highlight that walks each value into its cell in row-major order (C). Uses the embedding decision from Task 1.

**Files:**
- Create: `diagrams/row-major-grid.svg`
- Modify: `formats/coveragejson.qmd` (Section 4.3, replace the ASCII `x0 x1 x2 / y0 [1 2 3] / y1 [4 5 6]` block)

**Interfaces:**
- Consumes: Task 1's embedding decision (`![](...)` vs raw inline-SVG block); the B-bar conventions.
- Produces: the established motion-diagram pattern (CSS `@keyframes` + reduced-motion guard) the conventions doc (Task 6) describes.

- [ ] **Step 1: Author the SVG (static B layer + C highlight layer)**

Create `diagrams/row-major-grid.svg`:

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 520 270" role="img" aria-labelledby="rm-title rm-desc" font-family="-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif">
  <title id="rm-title">Row-major order: a flat values list folds onto a grid</title>
  <desc id="rm-desc">The flat values list one through six fills a two-row by three-column grid row by row, so the last axis x varies fastest. Values one, two, three form the first row y0; values four, five, six form the second row y1.</desc>
  <style>
    .hl { opacity: 0; animation: rm-pulse 6s linear infinite; }
    @keyframes rm-pulse { 0% {opacity:0} 3% {opacity:.55} 13% {opacity:.55} 17% {opacity:0} 100% {opacity:0} }
    @media (prefers-reduced-motion: reduce) { .hl { animation: none; opacity: 0; } }
  </style>

  <rect x="1" y="1" width="518" height="268" rx="16" fill="#ffffff" stroke="#e2e8f0" stroke-width="2"/>
  <text x="260" y="32" text-anchor="middle" font-size="13" fill="#64748b" font-family="ui-monospace, Menlo, monospace">values flatten row by row &#8212; last axis (x) varies fastest</text>

  <!-- flat strip: two colour-coded groups -->
  <g font-size="15" font-weight="700" text-anchor="middle" font-family="ui-monospace, Menlo, monospace">
    <g fill="#e0e7ff" stroke="#4338ca" stroke-width="1.5">
      <rect x="120" y="48" width="34" height="30" rx="5"/><rect x="162" y="48" width="34" height="30" rx="5"/><rect x="204" y="48" width="34" height="30" rx="5"/>
    </g>
    <g fill="#ccfbf1" stroke="#0f766e" stroke-width="1.5">
      <rect x="288" y="48" width="34" height="30" rx="5"/><rect x="330" y="48" width="34" height="30" rx="5"/><rect x="372" y="48" width="34" height="30" rx="5"/>
    </g>
    <g fill="#3730a3"><text x="137" y="68">1</text><text x="179" y="68">2</text><text x="221" y="68">3</text></g>
    <g fill="#0f766e"><text x="305" y="68">4</text><text x="347" y="68">5</text><text x="389" y="68">6</text></g>
  </g>

  <!-- brackets + row labels -->
  <path d="M120 84 v6 h118 v-6" fill="none" stroke="#4338ca" stroke-width="1.5"/>
  <path d="M288 84 v6 h118 v-6" fill="none" stroke="#0f766e" stroke-width="1.5"/>
  <text x="179" y="104" text-anchor="middle" font-size="11" font-weight="600" fill="#4338ca">row y0</text>
  <text x="347" y="104" text-anchor="middle" font-size="11" font-weight="600" fill="#0f766e">row y1</text>

  <!-- grid -->
  <text x="345" y="128" text-anchor="middle" font-size="11" fill="#64748b">x varies fastest &#8594;</text>
  <g font-size="12" fill="#64748b" text-anchor="middle" font-family="ui-monospace, Menlo, monospace"><text x="250" y="142">x0</text><text x="305" y="142">x1</text><text x="360" y="142">x2</text></g>
  <g font-size="12" font-weight="600" text-anchor="end" font-family="ui-monospace, Menlo, monospace"><text x="215" y="174" fill="#4338ca">y0</text><text x="215" y="220" fill="#0f766e">y1</text></g>
  <g fill="#eef2ff" stroke="#4338ca" stroke-width="1.5"><rect x="225" y="152" width="50" height="36" rx="6"/><rect x="280" y="152" width="50" height="36" rx="6"/><rect x="335" y="152" width="50" height="36" rx="6"/></g>
  <g fill="#f0fdfa" stroke="#0f766e" stroke-width="1.5"><rect x="225" y="196" width="50" height="36" rx="6"/><rect x="280" y="196" width="50" height="36" rx="6"/><rect x="335" y="196" width="50" height="36" rx="6"/></g>
  <g font-size="15" font-weight="700" text-anchor="middle" font-family="ui-monospace, Menlo, monospace">
    <g fill="#3730a3"><text x="250" y="175">1</text><text x="305" y="175">2</text><text x="360" y="175">3</text></g>
    <g fill="#0f766e"><text x="250" y="219">4</text><text x="305" y="219">5</text><text x="360" y="219">6</text></g>
  </g>

  <!-- C: amber highlight overlays (flat cell + matching grid cell pulse together, staggered row-major) -->
  <g fill="#f59e0b">
    <rect class="hl" style="animation-delay:0s" x="120" y="48" width="34" height="30" rx="5"/><rect class="hl" style="animation-delay:0s" x="225" y="152" width="50" height="36" rx="6"/>
    <rect class="hl" style="animation-delay:1s" x="162" y="48" width="34" height="30" rx="5"/><rect class="hl" style="animation-delay:1s" x="280" y="152" width="50" height="36" rx="6"/>
    <rect class="hl" style="animation-delay:2s" x="204" y="48" width="34" height="30" rx="5"/><rect class="hl" style="animation-delay:2s" x="335" y="152" width="50" height="36" rx="6"/>
    <rect class="hl" style="animation-delay:3s" x="288" y="48" width="34" height="30" rx="5"/><rect class="hl" style="animation-delay:3s" x="225" y="196" width="50" height="36" rx="6"/>
    <rect class="hl" style="animation-delay:4s" x="330" y="48" width="34" height="30" rx="5"/><rect class="hl" style="animation-delay:4s" x="280" y="196" width="50" height="36" rx="6"/>
    <rect class="hl" style="animation-delay:5s" x="372" y="48" width="34" height="30" rx="5"/><rect class="hl" style="animation-delay:5s" x="335" y="196" width="50" height="36" rx="6"/>
  </g>
</svg>
```

- [ ] **Step 2: Verify well-formedness and invariants**

Run:
```
xmllint --noout diagrams/row-major-grid.svg
grep -c 'stroke="#14b8a6"' diagrams/row-major-grid.svg
grep -c 'prefers-reduced-motion' diagrams/row-major-grid.svg
grep -c '<title' diagrams/row-major-grid.svg
```
Expected: no xmllint output; `0` light-teal strokes; `1` reduced-motion guard; `1` title.

- [ ] **Step 3: Replace the ASCII fold block in Section 4.3**

In `formats/coveragejson.qmd`, find the ```` ```text ```` block in Section 4.3 that draws:
```
            x0   x1   x2
   y0  [    1    2    3  ]
   y1  [    4    5    6  ]
```
Delete that fenced block and replace it with (use the embedding form from Task 1's decision):

```markdown
![Reading the flat `values` onto the grid: with `axisNames` `["y","x"]` the last-named axis, `x`, varies fastest, so the first three values fill row `y0` and the next three fill row `y1`.](../diagrams/row-major-grid.svg){#fig-row-major}
```

If Task 1 decided inline-SVG is required, instead paste the SVG inside a ```` ```{=html} ```` block wrapped in `<figure>`/`<figcaption>` at that location.

- [ ] **Step 4: Render and verify motion + both themes**

Run: `quarto render formats/coveragejson.qmd`
In `quarto preview`, open Section 4.3.
Expected: the amber highlight steps value-by-value into the grid in row-major order, looping; the static layer (brackets, colour-mapped rows, "x varies fastest") reads on both light and dark themes.

- [ ] **Step 5: Verify reduced-motion + grayscale/CVD fallback**

DevTools Rendering: set `prefers-reduced-motion: reduce` -> reload. Expected: highlight is gone, the static B frame fully conveys the row-major rule (brackets + colour-map + caption).
Then set "Emulate vision deficiencies" to `Achromatopsia` and `Deuteranopia`. Expected: rows remain distinguishable via the `row y0`/`row y1` brackets, `y0`/`y1` labels, and luminance — not color alone.

- [ ] **Step 6: Stage (Chuck commits)**

Run: `git add diagrams/row-major-grid.svg formats/coveragejson.qmd`
Then pause for Chuck to commit. Suggested message: `feat(diagrams): add animated row-major fold figure to CovJSON 4.3`

---

### Task 5: `coverage-as-spreadsheet.svg` (hero, B) + embed in Section 2.1

Add the net-new hero: a spreadsheet of cells laid over a simple map (positions = domain, cell values = range), plus a second panel of two stacked sheets sharing one set of row/column labels (the multiple-ranges idea). Composition is the most uncertain, so it gets a visual-companion mockup pass first.

**Files:**
- Create: `diagrams/coverage-as-spreadsheet.svg`
- Modify: `formats/coveragejson.qmd` (Section 2.1, after the "imagine an enormous spreadsheet laid over a map" paragraph)

**Interfaces:**
- Consumes: the B-bar conventions; the static-diagram embedding workflow from Task 3.
- Produces: the hero figure; no later task depends on its internals.

- [ ] **Step 1: Mock the composition in the visual companion**

Start (or reuse) the brainstorm companion server (it needs a real Node on PATH — the asdf shim is broken; use `~/.asdf/installs/nodejs/22.16.0/bin`). Push 2 candidate compositions to the companion: (A) one spreadsheet-over-map panel + a separate stacked-sheets panel side by side; (B) a single integrated panel where the stacked sheets sit over the map. Ask Chuck which composition to author. Record the choice.

- [ ] **Step 2: Author the SVG to the chosen composition**

Create `diagrams/coverage-as-spreadsheet.svg` following the B-bar: white card; a simple map silhouette in neutral slate; an indigo-tinted grid of positions overlaid (domain) with row/column position labels; teal-tinted cell values (range); a second element showing two stacked sheets sharing one set of row/column labels for the multiple-ranges idea. Include `role="img"` + `<title>`/`<desc>`. Use `#4338ca`/`#0f766e` for strokes and small text, light tints for fills, and label with the prose vocabulary: domain, range, positions, values. (Optional subtle C touch — a single cell's value briefly highlighting as it is "read" — only if it reads cleanly; otherwise keep static.)

- [ ] **Step 3: Verify well-formedness and invariants**

Run:
```
xmllint --noout diagrams/coverage-as-spreadsheet.svg
grep -c 'stroke="#14b8a6"' diagrams/coverage-as-spreadsheet.svg
grep -c '<title' diagrams/coverage-as-spreadsheet.svg
grep -c '<desc' diagrams/coverage-as-spreadsheet.svg
```
Expected: no xmllint output; `0` light-teal strokes; `1` title; `1` desc. (If a C touch was added, also `grep -c 'prefers-reduced-motion'` -> `1`.)

- [ ] **Step 4: Embed in Section 2.1**

In `formats/coveragejson.qmd`, Section 2.1, immediately after the paragraph ending "...bundled with enough description that software can read any cell unambiguously." add:

```markdown
![A coverage is a spreadsheet laid over a map: the row and column positions are the domain, the value in each cell is the range. Several properties measured at the same positions are stacked sheets sharing one set of row and column labels -- one shared domain, one range per sheet.](../diagrams/coverage-as-spreadsheet.svg){#fig-coverage-spreadsheet}
```

- [ ] **Step 5: Render and verify (both themes + grayscale/CVD)**

Run: `quarto render formats/coveragejson.qmd`
In `quarto preview`, open Section 2.1. Expected: the hero renders after the spreadsheet paragraph, reads on both themes; in DevTools `Achromatopsia`/`Deuteranopia`, domain vs range remain distinguishable via labels + position, not color alone.

- [ ] **Step 6: Stage (Chuck commits)**

Run: `git add diagrams/coverage-as-spreadsheet.svg formats/coveragejson.qmd`
Then pause for Chuck to commit. Suggested message: `feat(diagrams): add spreadsheet-over-map hero to CovJSON 2.1`

---

### Task 6: Codify the matured conventions in `diagrams/README.md`

Update the conventions doc so the next diagram (in any guide) is reproducible against the proven pattern.

**Files:**
- Modify: `diagrams/README.md`

**Interfaces:**
- Consumes: the patterns established in Tasks 1-5.

- [ ] **Step 1: Rewrite the conventions**

Update `diagrams/README.md` to document, with the exact values from Global Constraints:
- The **B-bar**: palette, light-card strategy (reads on both themes), Level-2 quality floor (explanatory device required), vocabulary parity.
- The **color/accessibility rules**: no-color-alone; luminance separation; the fill-tint-vs-darker-stroke contrast rule (`#14b8a6` never a stroke; use `#4338ca`/`#0f766e`); `role`/`title`/`desc` required.
- The **C motion layer**: CSS `@keyframes` inside the SVG; the `@media (prefers-reduced-motion: reduce)` guard; motion only where it teaches what static cannot; information parity with the static frame; the embedding decision from Task 1.
- The **file conventions**: hand-coded SVG committed as source, kebab-case names in `diagrams/`.
- Update the inventory list to include `coverage-as-spreadsheet.svg`, `row-major-grid.svg`, and `parameter-range-binding.svg`.

- [ ] **Step 2: Verify the doc lints clean and the site still builds**

Run:
```
prek run --files diagrams/README.md
quarto render
```
Expected: pre-commit hooks pass on the doc; full site renders with no errors.

- [ ] **Step 3: Run the offline link check (as CI will)**

Run (if `lychee` is installed; else note it runs in CI): `lychee --offline --include-fragments --no-progress './_site/**/*.html'`
Expected: no broken internal links/anchors (the new `#fig-*` references resolve).

- [ ] **Step 4: Stage (Chuck commits)**

Run: `git add diagrams/README.md`
Then pause for Chuck to commit. Suggested message: `docs(diagrams): codify B-bar + motion + accessibility conventions`

---

## Self-Review

**1. Spec coverage:**
- Scope diagram 1 (spreadsheet hero) -> Task 5. Diagram 2 (row-major) -> Task 4. Diagram 3 (binding) -> Task 3. README conventions -> Task 6. ✓
- `coverage-as-function.svg` teal-stroke fix (spec §5/§8/§12) -> Task 2. ✓
- Keep `coverage-as-function` + add spreadsheet -> Task 5 Step 4 (add, not replace). ✓
- B-bar (spec §4) -> Global Constraints + every author step. ✓
- Color/accessibility rules (spec §5) -> Global Constraints + verification steps (grayscale/CVD) in Tasks 3-5. ✓
- Motion system (spec §6) -> Task 1 (spike) + Task 4 (CSS-in-SVG + reduced-motion guard). ✓
- Content integration / ASCII removal (spec §8) -> Tasks 3-5 replace steps; out-of-scope ASCII untouched. ✓
- Early CSS-in-SVG spike (spec §10) -> Task 1, ordered first. ✓
- Success criteria (spec §10): both-theme render, reduced-motion freeze, title/desc, grayscale/CVD, contrast, ASCII removed, README, clean build + link check -> covered across task verification steps and Task 6. ✓

**2. Placeholder scan:** No "TBD"/"add appropriate X"/"similar to Task N". The two well-specified diagrams (Tasks 3, 4) ship complete SVG. Task 5's SVG is authored during execution *because* the spec defers its composition to a companion mockup; its brief, invariants, and acceptance checks are concrete. This is a deliberate, spec-sanctioned exception, not a placeholder.

**3. Type consistency:** Figure ids are unique and stable: `#fig-parameter-range-binding` (Task 3), `#fig-row-major` (Task 4), `#fig-coverage-spreadsheet` (Task 5). SVG filenames match between create steps, embed steps, and the Task 6 inventory. Color tokens match Global Constraints throughout.
