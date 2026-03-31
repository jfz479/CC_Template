---
name: review-julia
description: Code review dispatching the Debugger agent in standalone mode (categories 4-12 only). Checks type stability, numerical guards, JLD2 saves, CairoMakie figures, reproducibility, and professional polish. Julia primary, Stata secondary. Use for standalone code quality review without strategy memo.
argument-hint: "[filename, directory, or 'all']"
allowed-tools: ["Read", "Grep", "Glob", "Write", "Task"]
---

# Review Code Scripts

Run the code review protocol by dispatching the **Debugger** agent in standalone mode.

In standalone mode, the Debugger runs **categories 4-12 only** (code quality). Categories 1-3 (strategic alignment) require a strategy memo and run only within the pipeline or via `/model-check`.

## Workflow

### Step 1: Identify Scripts

- If `$ARGUMENTS` is a specific `.jl` file: review that file
- If `$ARGUMENTS` is a specific `.do` file: review that file
- If `$ARGUMENTS` is `all`: review all scripts in `Model/src/`, `Model/scripts/`, `scripts/julia/`, `scripts/stata/`
- If `$ARGUMENTS` is a directory (e.g., `Model/src`): review all scripts in that directory
- If no argument: default to `Model/src/` (the core structural code)

### Step 2: Read Conventions

Before launching the Debugger, read:
1. `.claude/rules/julia-code-conventions.md` (for .jl files)
2. `Model/Project.toml` (to know which packages are available)

### Step 3: Launch Debugger Agent

For each script or batch, delegate to the `debugger` agent via Task tool:

```
Prompt: Review [file] in standalone mode (categories 4-12 only).

Julia categories:
  4. Script structure (headers, module organization, Project.toml activation)
  5. Output hygiene (no println in hot paths, @info for progress, VFI convergence reporting)
  6. Reproducibility (Random.seed!, relative paths via joinpath/@__DIR__, Manifest.toml)
  7. Type and function design (@kwdef struct with Float64, snake_case, ! suffix, no magic numbers)
  8. Figure quality (CairoMakie, paper/Beamer dimensions, transparent bg, labels with units)
  9. JLD2 save pattern (estimation results, VFI solution, simulation output)
  10. Comments (WHY not WHAT, economic intuition, calibration sources, equation cross-refs)
  11. Numerical guards (NaN/Inf checks, convergence tolerance, max_iter, log domain, optimizer bounds)
  12. Polish (4-space indent, import vs using in src/, no global state, no type piracy)

Stata categories (if .do files in scope):
  4. Script structure (header block, version, clear all, log file)
  5. Output hygiene (quietly, noisily, log capture)
  6. Reproducibility (set seed, relative paths, sort stable)
  7. Program and variable design (tempvars, merge diagnostics, no magic numbers)
  8. Figure quality (scheme, graph export, labels with units)
  9. Save pattern (dta files, estimates save, moment CSV export to Data/moments/)
  10. Comments (WHY not WHAT, sample restriction rationale)
  11. Error handling (assert, capture, merge checks, missing data counts)
  12. Polish (no abbreviations, consistent indent, labels on all variables)

Check the known pitfalls tables in the debugger agent for language-specific traps.
Save report to quality_reports/[script_name]_code_review.md
```

### Step 4: Present Summary

After all reviews complete:
- Total issues found per script
- Breakdown by severity (Critical / Major / Minor)
- Top 3 most critical issues across all scripts
- Code review score
- Performance concerns flagged separately (type stability, allocation, convergence)

### Step 5: IMPORTANT

**Do NOT edit any source files.** Only produce reports. Fixes are applied after user review, either manually or by re-dispatching the Coder agent.

---

## Principles

- **Standalone mode = code quality only.** Model-strategy alignment requires a strategy memo and runs in the full pipeline.
- **Performance is not optional.** Type instability in a VFI loop is Critical, not a style preference. Structural estimation runs for hours; a 10x slowdown is a real cost.
- **Language-aware.** Apply Julia checks to .jl files, Stata checks to .do files. Do not flag Julia idioms in Stata or vice versa.
- **Proportional severity.** A missing `Random.seed!` is Major. A missing comment is Minor. Type instability in a helper called once is Minor; in the VFI inner loop it is Critical.
- **Worker-critic separation.** The Debugger never fixes code. It only critiques.
