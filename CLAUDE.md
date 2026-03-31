# CLAUDE.MD -- Structural Macro-Labor Research with Claude Code

**Project:** [YOUR PROJECT NAME]
**Institution:** European University Institute (EUI)
**Branch:** main
**Primary language:** Julia
**Secondary languages:** Stata, Matlab (infrequent), R (infrequent)

---

## Core Principles

- **Plan first** -- enter plan mode before non-trivial tasks; save plans to `quality_reports/plans/`
- **Verify after** -- compile and confirm output at the end of every task
- **Single source of truth** -- Paper `main.tex` is authoritative; talks and supplements derive from it
- **Quality gates** -- weighted aggregate score; nothing ships below 80/100; see `quality.md`
- **Worker-critic pairs** -- every creator has a paired critic; critics never edit files
- **[LEARN] tags** -- when corrected, save `[LEARN:category] wrong → right` to MEMORY.md

---

## Folder Structure

```
[YOUR-PROJECT]/
├── CLAUDE.MD                    # This file
├── .claude/                     # Rules, skills, agents, hooks
├── Bibliography_base.bib        # Centralised bibliography
├── Paper/                       # Main LaTeX manuscript (source of truth)
│   ├── main.tex                 # Primary paper file
│   ├── paper.sty                # Custom style file
│   └── sections/                # Section-level .tex files
├── Talks/                       # Derivative Beamer presentations (XeLaTeX)
│   ├── job_market_talk.tex
│   ├── seminar_talk.tex
│   ├── short_talk.tex
│   └── lightning_talk.tex
├── Model/                       # Structural model code (Julia)
│   ├── src/                     # Core model modules
│   │   ├── types.jl             # Parameter structs, model types
│   │   ├── setup.jl             # Grid construction, calibration
│   │   ├── vfi.jl               # Value function iteration
│   │   ├── simulation.jl        # Simulation / stationary distribution
│   │   ├── moments.jl           # Moment computation for SMM
│   │   ├── estimation.jl        # SMM objective, CMA-ES wrapper
│   │   └── helpers.jl           # Utility functions
│   ├── scripts/                 # Execution scripts
│   │   ├── run_estimation.jl
│   │   ├── run_simulation.jl
│   │   └── run_counterfactual.jl
│   ├── Project.toml
│   └── Manifest.toml
├── Data/                        # Project data
│   ├── raw/                     # Original untouched (gitignored)
│   ├── cleaned/
│   └── moments/                 # Empirical moments for SMM targets
├── scripts/                     # Non-Julia analysis code
│   ├── stata/                   # Stata .do files
│   └── julia/                   # Standalone Julia scripts (plots, tables)
├── Output/                      # Intermediate results
├── Figures/                     # Final figures for paper
├── Tables/                      # Final tables (.tex) for paper
├── Supplementary/               # Online appendix
├── Replication/                 # Replication package
├── Preambles/                   # Shared LaTeX preambles + Beamer theme
├── quality_reports/
├── explorations/
├── onboarding_material		 # Source of material if project was started elsewhere
└── master_supporting_docs/
```

---

## Compilation Commands

**Paper (LaTeX):**
```bash
cd Paper && latexmk -pdf main.tex
```

**Talks (XeLaTeX):**
```bash
cd Talks && TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode $FILE.tex
BIBINPUTS=..:$BIBINPUTS bibtex $FILE
TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode $FILE.tex
TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode $FILE.tex
```

**Julia model:**
```bash
cd Model && julia --project=. scripts/run_estimation.jl
```

---

## Skills Quick Reference

### Consolidated Skills (v3.0 — sub-modes reduce token load ~60%)

| Command | What It Does |
|---------|-------------|
| `/discover interview [topic]` | Interactive research interview → spec + domain profile |
| `/discover literature [topic]` | Librarian + Editor: literature search + synthesis |
| `/discover data [question]` | Explorer + Surveyor: dataset discovery + assessment |
| `/discover ideation [topic]` | Generate research questions + strategies |
| `/review --paper [file]` | Multi-agent parallel review + weighted score |
| `/review --peer [journal]` | Domain + Methods Referee + Editor: simulated peer review |
| `/review --proofread [file]` | Proofreader: 6-category manuscript review |
| `/review --code [file]` | Debugger: Julia/Stata code quality review |
| `/review --visual [file]` | Slide layout audit |
| `/review --replicate [dir]` | Verifier: replication package check |
| `/review --all` | Full review pipeline |
| `/tools compile [file]` | Paper: latexmk; Talks: 3-pass XeLaTeX + bibtex |
| `/tools validate-bib` | Cross-reference citations |
| `/tools commit [msg]` | Stage, commit, PR, merge |

### Standalone Skills

| Command | What It Does |
|---------|-------------|
| `/new-project [topic]` | Full pipeline: idea → paper (orchestrated) |
| `/onboard [stage]` | Migrate existing work into template pipeline |
| `/identify [question]` | Strategist + Structural Modeler: model design |
| `/structural-estimation` | End-to-end estimation workflow guidance |
| `/data-analysis [dataset]` | Coder + Debugger: empirical moments, data work |
| `/draft-paper [section]` | Writer: draft paper sections + humanizer pass |
| `/target-journal [paper]` | Editor: journal targeting + submission strategy |
| `/respond-to-referee [report]` | Point-by-point referee response |
| `/create-talk [format]` | Storyteller + Discussant: Beamer talk from paper |
| `/humanizer [file]` | Strip 24 AI writing patterns |
| `/submit [journal]` | Final gate: score >= 95, all components >= 80 |

---

## Beamer Custom Environments (Talks)

| Environment       | Effect        | Use Case       |
|-------------------|---------------|----------------|
| `[your-env]`      | [Description] | [When to use]  |

---

## Notation Conventions

| Symbol | Meaning | Context |
|--------|---------|---------|
| [Variable symbol] | [Meaning] | [Context]

---

## Current Project State

| Component | File | Status | Description |
|-----------|------|--------|-------------|
| Paper | `Paper/main.tex` | [status] | [description] |
| Model | `Model/src/` | [status] | [description] |
| Data | `scripts/stata/` | [status] | [description] |
| Replication | `Replication/` | [status] | [status] |
| Job Market Talk | `Talks/job_market_talk.tex` | -- | -- |
