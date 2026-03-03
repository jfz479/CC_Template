---
name: data-analysis
description: End-to-end data analysis dispatching the Coder agent for implementation and Debugger agent for code review. Supports R, Stata, Python, and Julia. Produces scripts, tables, figures, and results summary.
disable-model-invocation: true
argument-hint: "[dataset path or description of analysis goal]"
allowed-tools: ["Read", "Grep", "Glob", "Write", "Edit", "Bash", "Task"]
---

# Data Analysis Workflow

Run an end-to-end data analysis by dispatching the **Coder** (implementer) and **Debugger** (code critic).

**Input:** `$ARGUMENTS` — a dataset path (e.g., `Data/cleaned/panel.csv`) or a description of the analysis goal.

---

## Workflow

### Step 1: Context Gathering

1. Read `.claude/rules/domain-profile.md` for field conventions
2. Read any strategy memo in `quality_reports/` (if analysis implements a pre-specified design)
3. Check `CLAUDE.md` for language preference (R/Stata/Python/Julia)
4. Scan existing scripts in `scripts/Julia/` (or `scripts/stata/`, etc.) for project patterns

### Step 2: Launch Coder Agent

Delegate to the `coder` agent via Task tool:

```
Prompt: Implement analysis for "[goal]" using data at "[path]".
Follow 4 stages:
  Stage 0: Data cleaning (if raw data provided)
  Stage 1: Main specification (from strategy memo or user description)
  Stage 2: Robustness checks
  Stage 3: Publication-ready output (tables to Tables/, figures to Figures/)
Produce results_summary.md with all estimates, SEs, and key statistics.
Save scripts to scripts/R/ (or appropriate language directory).
```

The Coder follows these principles:
- **Script structure:** Header, setup, data loading, analysis, output, export
- **Packages:** in Stata: eststo and estout N/A, in Julia: CairoMakie for figures. Own code over user-written packages. Ask permission if new package is proposed for installation.
- **Output:** `.tex` tables for LaTeX (see Paper/main.tex and Paper/paper.sty for compatibility), `.pdf`/`.png` figures, `.rds` for intermediate objects
- **No hardcoded paths.** All paths relative to repository root.

### Step 3: Launch Debugger Agent (Code Critic)

After Coder returns, delegate to the `debugger` agent:

```
Prompt: Review the script(s) at scripts/Stata/[script_name].do.
Run all 12 check categories:
  Strategic (1-3): code-strategy alignment, sanity checks, robustness sufficiency
  Code Quality (4-12): structure, console hygiene, reproducibility, functions,
    figure quality, RDS pattern, comments, error handling, polish
If strategy memo exists, cross-reference code against stated design.
Save report to quality_reports/[script]_code_review.md
```

### Step 4: Fix Issues

If Debugger finds Critical or Major issues:
1. Re-dispatch Coder with specific fixes (max 3 rounds per `three-strikes.md`)
2. Re-run Debugger to verify fixes

### Step 5: Present Results

Show the user:
1. **Results summary** — key estimates with SEs and interpretation
2. **Scripts created** — paths and descriptions
3. **Output files** — tables in `Tables/`, figures in `Figures/`
4. **Code review score** — from Debugger
5. **Any TODO items** — missing data, additional specifications needed

---

## Script Structure Template

```stata
* ============================================================
* [Descriptive Title]
* Author: [from project context]
* Purpose: [What this script does]
* Inputs: [Data files]
* Outputs: [Tables, CSV files]
* ============================================================

* 0. Setup ----
clear all
set more off
set seed 42

global root "/path/to/project"
global data "${root}/Data"
global tables "${root}/Tables"
global figures "${root}/Figures"

capture mkdir "${root}/Tables"
capture mkdir "${root}/Figures"

* 1. Data Loading ----

* 2. Exploratory Analysis ----

* 3. Main Analysis ----

* 4. Tables ----

* 5. Export ----
```

```julia
# ============================================================
# [Descriptive Title]
# Author: [from project context]
# Purpose: [What this script does]
# Inputs: [Data files]
# Outputs: [Figures]
# ============================================================

using CSV
using DataFrames
using CairoMakie

# Load results from Stata CSV
results = CSV.read("Tables/[Name of csv file for figure].csv", DataFrame)

# Example: create a simple bar plot of means
fig = Figure(resolution=(800, 600))
ax = Axis(fig[1, 1], 
    xlabel="Variables", 
    ylabel="Mean Value",
    title="Summary Statistics from Stata Analysis")

variables = ["y", "x1", "x2"]
means = results.y[1:3]  # Adjust based on your CSV structure

barplot!(ax, 1:length(variables), means, color=:steelblue)
ax.xticks = (1:length(variables), variables)

save("Figures/results_plot.png", fig)
fig
```


---

## Principles

- **Reproduce, don't guess.** If the user specifies a regression, run exactly that.
- **Show your work.** Print summary statistics before jumping to regressions.
- **Strategy alignment.** If a strategy memo exists, the code MUST implement it faithfully.
- **Worker-critic pairing.** Coder creates, Debugger critiques. Never skip the review.
- **saveRDS everything.** Every computed object gets saved for downstream use.
- **Publication-ready output.** Tables and figures should be directly includable in the paper.
