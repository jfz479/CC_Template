# Working Paper Format Standard

**Based on `Paper/paper.sty`.** This reference codifies the formatting conventions so agents know them without reading the `.sty` file. The `.sty` file is authoritative — this document summarizes its choices.

---

## Document Class & Layout

- `\documentclass[a4paper,11pt,fleqn]{article}`
- Margins: 1.1in all sides, footskip 0.7in
- One-and-a-half spacing (`\usepackage[onehalfspacing]{setspace}`)
- French spacing (`\frenchspacing`)
- Left-aligned equations (`fleqn`)

## Fonts

- **Body:** XCharter (`\usepackage{XCharter}`)
- **Math:** newtxmath with xcharter option
- **Monospace:** Source Code Pro
- **Math text:** italic via mathastext
- Encoding: T1

## Title Page

- Title: `\huge\bfseries`, centered
- Authors: `\author{Name \\ \textit{Institution} \and Name2 \\ \textit{Institution2}}`
- **Co-author ordering: alphabetical** unless otherwise specified
- No thanks mark (removed via `\renewcommand{\tamark}{}`)
- Abstract on title page, no indentation
- Title page has empty page style (no page number)

## Headings

- `\section`: centered, `\large\bfseries`
- `\subsection`: `\bfseries` (left-aligned)
- `\paragraph`: italic, run-in, ends with period
- Label format: `1.` `2.` etc. (with period and quad space)

## Tables & Figures

- Three-line format: `\toprule` / `\midrule` / `\bottomrule` (booktabs)
- **No vertical rules ever**
- Array stretch: 1.1
- Tabular body: `\footnotesize` (automatic via `BeforeBeginEnvironment`)
- Display math arrays: `\displaystyle` automatic
- Caption labels: `\textsc{Figure}` and `\textsc{Table}` with period separator
- Subcaptions: letters (A, B, C), footnotesize, period separator
- Table captions: position above (`position=top`)
- All floats auto-centered
- Notes below: `\note[prefix]{text}` macro (footnotesize, 10pt vspace)
- Figure spacing: `\fspace` macro (4mm vertical)

## Lists

- No extra spacing (`nosep`)
- Itemize: gray bullet, parindent left margin
- Enumerate: gray lowercase letters (`a.`, `b.`)
- Nested: endash / roman numerals, 2x parindent

## Theorems & Proofs

- Style: italic body, smallcaps label, period after name
- Environments: theorem, proposition, lemma, corollary, definition, assumption, remark
- Proof label: upshape smallcaps

## Bibliography

- Style: apacite (classic)
- Font: `\small` in bibliography, `\footnotesize` with 1.1 line stretch
- File: defined via `\newcommand{\bib}{bibliography.bib}` in main.tex

## Headers & Footers

- No header rule
- Centered page numbers on regular pages
- Empty headers/footers on title page
- Optional `\available[journal]{url}` footer on title page

## Hyperlinks

- Hidden links (`hidelinks`)
- PDF metadata: `hypertexnames=false`, `pdfpagemode=UseNone`

## Appendix

- Counters reset after bibliography (theorems, figures, tables, equations)
- Prefix with A: `A1`, `A2`, etc.
- Section format: `Appendix A.` centered

## Figures File

- Figures stored in a single PDF: `\newcommand{\pdf}{figures.pdf}`
- Or individual files in `Figures/` directory

## What NOT to Change

The Writer agent must NOT:
- Switch to biblatex/biber (project uses apacite/bibtex)
- Add packages that conflict with XCharter or newtxmath
- Change margin or spacing settings
- Add custom commands that duplicate existing macros (`\note`, `\fspace`)
- Use `\hline` or vertical rules in tables
- Bold table body content
