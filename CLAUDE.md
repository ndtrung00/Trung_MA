# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

LaTeX source for a **Master's thesis (M.Sc.)** at TUM, Chair of Information Systems Development and Operation (ISDO), supervised by Samed Bayer. Topic area: **Generative AI for Research**. Based on the (now-deprecated) `fwalch/tum-thesis-latex` template; the current maintained upstream is `TUM-Dev/tum-thesis-latex` — check it before making structural template changes. The chair's own rules live in `Thesis_Guideline-7.pdf` (extracted below).

## Build commands

Requires `latexmk`, `pdflatex`, and `biber` (thesis uses `biblatex` with the `biber` backend). Output goes to `build/`.

- `make pdf` — one-shot build of `main.pdf`.
- `make watch` — `latexmk -pvc`; rebuilds continuously on save.
- `make clean` — delete build artifacts except `build/main.pdf`.
- `make purge` — delete the entire `build/` directory.

CI runs `make pdf` inside the `texlive/texlive:latest` container on a Blacksmith runner (`.github/workflows/github-actions-demo.yml`). The Blacksmith GitHub App must be installed on the repo for the runner label to resolve.

## Architecture

`main.tex` is the master document and the only file compiled directly. It:

1. Loads `settings.tex` (packages, TUM colors, biblatex config, pgfplots/listings styles).
2. Defines thesis metadata via `\newcommand*` (title, author, supervisor, submission date, etc.) — these are consumed by `pages/title.tex` and `pages/cover.tex`.
3. Includes front matter from `pages/` (cover, title, disclaimer, acknowledgments, abstract).
4. Includes body chapters from `chapters/01_introduction.tex` through `chapters/07_conclusion.tex`.
5. Emits lists of figures/tables and the bibliography via `\printbibliography`.

`\documentclass` has two alternative invocations commented in `main.tex` (one-sided vs. two-sided `scrbook`) — switching sides means editing `main.tex`, not a flag.

Citations live in `bibliography.bib`. The biblatex configuration (`style=alphabetic`, `maxnames`, etc.) is in `settings.tex` — change citation style there, not in `main.tex`.

`.latexmkrc` wires a `makeglossaries` custom dependency so latexmk can resolve a glossary if one is added; there is no glossary source file checked in yet.

## Template conventions

- `TODO` comments in `main.tex` and `settings.tex` mark the fields a new author must fill in (thesis title, author, advisor, citation style, one- vs. two-sided). Preserve these markers when editing — they're the intended onboarding trail.
- TUM corporate-design colors are predefined in `settings.tex` (`TUMBlue`, `TUMAccentOrange`, etc.) and wired into the default `pgfplots` cycle list. Use these rather than ad-hoc colors for plots/diagrams.
- TUM and faculty logos are **not** committed (copyright). Place `logos/tum.pdf` and `logos/faculty.pdf` locally, then run `./crop-logos.sh` (or `crop-logos.cmd` on Windows) to tight-crop them before building. `logos/*.pdf` is gitignored.
- `.gitignore` whitelists `build/main.pdf` so the sample output can be committed; everything else under `build/` is ignored.

## Thesis writing guidelines (from `Thesis_Guideline-7.pdf`)

These rules come from the ISDO chair and apply when writing or editing prose in `chapters/` and `pages/`. They take precedence over generic academic-writing advice.

### M.Sc. scope bar

This is a Master's thesis, not a Bachelor's. The work must go **beyond application of an existing method**: novel element required — new approach, non-trivial modification to an existing architecture, or a deep analytical study. Every chapter should ultimately support a **clearly defined contribution that distinguishes the work from SOTA**. Replication-only framings are B.Sc.-level and should be rejected.

### Structural conventions

- **Abstract** (`pages/abstract.tex`): a summary, not a teaser. Must contain context, problem, methodology, and **outcomes** (actual results, not promises).
- **Introduction** (`chapters/01_introduction.tex`): use the **funnel** — Context → Problem → Current Methods → Gap → Contribution. Narrow from broad to specific.
- **Motivation / problem statement**: cite the **original seminal papers**, not survey papers that cite them. Include related publications that differentiate the work.
- **Methodology** (`chapters/03_methodology.tex`): describe exactly what was done, at a level where someone in a different field could reproduce it. Use scientific phrasing ("to investigate whether X can improve Y") rather than definitive promises ("I will improve Y").
- **Conclusion** (`chapters/07_conclusion.tex`): must directly answer the Research Questions posed in the Introduction. This is the sanity-check linkage — RQs in, answers out.

### Paragraph and clarity rules

- One paragraph = one idea. Open with a **topic sentence** that states the paragraph's claim; close with a conclusion or transition, not a generality.
- Avoid marketing language ("92% of companies say…" without citation) and strong adjectives ("unique", "novel") unless scientifically proven.
- Keep tense, capitalization, and hyphenation consistent throughout the document.

### GenAI-specific rules

The topic is GenAI research, so the following are non-negotiable:

- **Reproducibility logging**: document prompts, model versions, random seeds, environment/library versions, and any stochastic settings. Non-determinism is expected; undocumented non-determinism is a defect.
- **Cross-validation of AI-generated content**: anything produced by an LLM that appears in the thesis (claims, citations, code) must be cross-validated against empirical evidence or domain expertise before it is committed. Do not cite hallucinated references.
- **AI-writing disclosure**: if GenAI tools are used for writing assistance, this must be explicitly declared in the thesis. The author remains responsible for every word — "the AI wrote it" is not a valid defense for hallucinations or plagiarism.
- **Accountability over polish**: negative results and limitations must be reported, not hidden. Bad results are still science.

### Evaluation standards (`chapters/05_results.tex`, `chapters/06_discussion.tex`)

Evaluation is the most common weak point and is judged strictly:

- The evaluation must **scientifically validate the answers to the RQs** — not merely show that code runs or that an output was produced.
- **Validation over observation**: measure *how well* the method works against the scientific challenge, not just *that* it works.
- **Baselines are mandatory**: compare against at least one of random/naive, SOTA, or a non-LLM solution — whichever is defensible for the RQ.
- **Dataset**: defined, explained, and logged (provenance, size, splits).
- **Metrics**: established quantitative or qualitative metrics, with the choice **justified** (why this metric for this RQ).
- **Uncertainty**: report variance, confidence intervals, or another uncertainty measure — a single run is not a result.
- **Limitations & negative results**: a dedicated discussion of what did not work and why.

### Final sanity checks before submission

Cross-reference against the checklist on page 3 of the guideline:
- Consistency of capitalization, hyphens, terminology, tense.
- All figures referenced in text ("As shown in Figure 1…"), axis labels and captions readable.
- Every external finding cited faithfully in a consistent format.
- Reproducibility artifacts (code, env logs, prompts, seeds) documented.
- Conclusion answers the Introduction's RQs.

Submission is via the TUM thesis portal; no physical copy is needed for the chair.
