---
name: onboard
description: Migrate an existing project into the template pipeline. Accepts files from any stage (literature, strategy, code, data, draft), places them in the correct template locations, dispatches critics to score each artifact, and reports which pipeline phases are complete. Use when picking up a project that already has work done outside this template.
disable-model-invocation: true
argument-hint: "[stage: 'literature', 'strategy', 'code', 'data', 'draft', 'all', or 'scan']"
allowed-tools: ["Read", "Grep", "Glob", "Write", "Edit", "Bash", "Task"]
---

# Project Onboarding

Migrate existing research artifacts into the template pipeline. Place files where the dependency graph expects them, score each artifact with the appropriate critic, and report pipeline readiness.

**Input:** `$ARGUMENTS` -- a stage name (`literature`, `strategy`, `code`, `data`, `draft`, `all`) or `scan` to auto-detect what exists.

---

## Principles

1. **Move, do not rewrite.** Place files in the right locations and add missing headers. Do not refactor code, rewrite prose, or change variable names. That is the Debugger's and Proofreader's job in the next review cycle.
2. **Score everything.** An artifact without a score cannot pass a quality gate. The dependency graph needs scores to know what is ready.
3. **Minimal disruption.** Copy, do not move. The user's originals stay where they are. Template locations get copies (or symlinks if the user prefers).
4. **Be honest about gaps.** If the user has code but no strategy memo, say so. The pipeline can still work, but the Debugger cannot run strategic alignment checks (categories 1-3) without a memo.

---

## Workflow

### Step 1: Inventory

If `$ARGUMENTS` is `scan` or `all`, scan the repository for existing work in the folder onboarding_material:

```
Check for:
  Literature:
    - Any .bib files outside Bibliography_base.bib
    - Any literature review documents (PDF, .md, .tex, .docx)
    - Papers in master_supporting_docs/
  Strategy:
    - Strategy memos, identification documents, model descriptions
    - Any document describing the model, moments, or estimation approach
  Code:
    - Julia files (.jl) outside Model/
    - Stata files (.do) outside scripts/stata/
    - Matlab files (.m)
    - Any estimation, simulation, or data cleaning scripts
  Data:
    - Data files in unexpected locations
    - Empirical moment tables (CSV, Excel, .dta)
    - Codebooks or data documentation
  Draft:
    - LaTeX files outside Paper/
    - Word documents, markdown drafts
    - Existing paper sections
  Talks:
    - Beamer files outside Talks/
    - PowerPoint or Keynote files
```

Present the inventory to the user. Ask which items to onboard.

If `$ARGUMENTS` is a specific stage, skip to that stage's workflow below.

### Step 2: Map and Place

For each artifact the user wants to onboard, copy it to the template location. Do not modify content except to add missing headers where noted.

#### Literature

| Source | Destination | Action |
|--------|-------------|--------|
| Existing .bib entries | `Bibliography_base.bib` | Merge entries (deduplicate by citation key) |
| Literature review doc | `quality_reports/literature_synthesis.md` | Copy; add YAML header if missing |
| Supporting papers | `master_supporting_docs/supporting_papers/` | Copy PDFs |

If a literature review exists but has no structured format, convert to template format:

```markdown
# Literature Synthesis: [Topic]
**Date onboarded:** [YYYY-MM-DD]
**Source:** Migrated from [original location]

## Key Papers
[extracted from user's document]

## Gaps Identified
[extracted or marked as TODO]

## Relationship to This Project
[extracted or marked as TODO]
```

#### Strategy

| Source | Destination | Action |
|--------|-------------|--------|
| Model description doc | `quality_reports/strategy/[project]/strategy_memo.md` | Copy; restructure headings if needed |
| Identification notes | `quality_reports/strategy/[project]/identification_table.md` | Copy or create from content |
| Computational notes | `quality_reports/strategy/[project]/computational_plan.md` | Copy or create from content |
| Calibration table | `quality_reports/strategy/[project]/calibration_table.md` | Copy |

If the user has a single document covering model + identification + computation, split it into the four template files (strategy_memo, identification_table, computational_plan, counterfactual_design). Preserve all original content; just redistribute under the correct headings.

If no formal strategy memo exists but the user describes the model verbally or has it embedded in a paper draft, extract the relevant sections and create the strategy files. Mark any gaps as `[TODO: specify]`.

#### Code

| Source | Destination | Action |
|--------|-------------|--------|
| Julia model code | `Model/src/` | Copy; check for module structure |
| Julia scripts | `Model/scripts/` | Copy |
| Julia standalone | `scripts/julia/` | Copy |
| Stata do-files | `scripts/stata/` | Copy |
| Matlab files | `scripts/matlab/` | Copy |
| Project.toml | `Model/Project.toml` | Copy (do not overwrite if exists) |
| Manifest.toml | `Model/Manifest.toml` | Copy (do not overwrite if exists) |

**Do not restructure code.** If the user has a single `model.jl` instead of the template's `types.jl | setup.jl | vfi.jl | ...` split, copy it as-is. The Debugger will flag the monolithic structure, and the user can refactor later.

**Do not rename variables.** If the user uses `camelCase` instead of `snake_case`, copy as-is. The Debugger catches this.

If the code has no `Project.toml`, create a minimal one:

```toml
[deps]
# TODO: add dependencies from the user's import/using statements
```

#### Data

| Source | Destination | Action |
|--------|-------------|--------|
| Raw data files | `Data/raw/` | Copy (never modify raw data) |
| Cleaned datasets | `Data/cleaned/` | Copy |
| Empirical moment files | `Data/moments/` | Copy |
| Codebooks | `Data/raw/` or `master_supporting_docs/` | Copy |

#### Draft

| Source | Destination | Action |
|--------|-------------|--------|
| Main paper .tex | `Paper/main.tex` | Copy (do not overwrite if exists) |
| Paper sections .tex | `Paper/sections/` | Copy |
| Separate .sty file | `Paper/paper.sty` | Copy |
| Preamble files | `Preambles/` | Copy |
| .bib from paper dir | Merge into `Bibliography_base.bib` | Merge |
| Word/markdown draft | `Paper/sections/` as .tex | Convert if straightforward; otherwise copy to `master_supporting_docs/` and flag for manual conversion |

#### Talks

| Source | Destination | Action |
|--------|-------------|--------|
| Beamer .tex files | `Talks/` | Copy |
| Theme files | `Preambles/` | Copy |
| Talk figures | `Figures/` (if shared) or `Talks/figures/` | Copy |

### Step 3: Update CLAUDE.md

After placing files, update the **Current Project State** table in `CLAUDE.md`:

```markdown
## Current Project State

| Component | File | Status | Description |
|-----------|------|--------|-------------|
| Paper | `Paper/main.tex` | onboarded | [brief description] |
| Model | `Model/src/` | onboarded | [brief description] |
| Data | `scripts/stata/` | onboarded | [brief description] |
| Replication | `Replication/` | not started | -- |
| Job Market Talk | `Talks/job_market_talk.tex` | onboarded | [brief description] |
```

### Step 4: Score Artifacts

Dispatch the appropriate critic for each onboarded artifact. This is the essential step: without scores, the dependency graph cannot advance.

**Literature** (if onboarded):
```
Dispatch: editor agent
Prompt: Review the literature synthesis at quality_reports/literature_synthesis.md.
  This was migrated from an existing project, not produced by the Librarian.
  Score coverage, recency, gaps, and relevance to the research question.
  Save report to quality_reports/literature_synthesis_review.md
```

**Strategy** (if onboarded):
```
Dispatch: structural-modeler agent
Prompt: Review the strategy memo at quality_reports/strategy/[project]/strategy_memo.md.
  This was migrated from an existing project, not produced by the Strategist.
  Run all 4 phases: model class, identification, computation, economics.
  Flag any sections marked [TODO: specify].
  Save report to quality_reports/strategy_memo_onboard_review.md
```

**Code** (if onboarded):
```
Dispatch: debugger agent
Prompt: Review the code in Model/src/ and scripts/.
  This was migrated from an existing project, not produced by the Coder.
  Run categories 4-12 (standalone mode).
  If a strategy memo exists in quality_reports/strategy/, also run categories 1-3.
  Note: code may not follow template conventions yet. Flag convention
  violations but weigh them as Minor unless they affect correctness or performance.
  Save report to quality_reports/onboard_code_review.md
```

**Draft** (if onboarded):
```
Dispatch: proofreader agent
Prompt: Review Paper/main.tex (or Paper/sections/*.tex).
  This was migrated from an existing project, not produced by the Writer.
  Run all 6 check categories.
  Save report to quality_reports/onboard_proofread_report.md
```

**Talks** (if onboarded):
```
Dispatch: discussant agent
Prompt: Review Talks/*.tex.
  Migrated from existing project.
  Check notation consistency with Paper/ if it exists.
  Advisory scoring only.
  Save report to quality_reports/onboard_talk_review.md
```

### Step 5: Pipeline Status Report

After all critics return, produce the pipeline status report:

```markdown
# Onboarding Report: [Project Name]
**Date:** [YYYY-MM-DD]

## Artifacts Onboarded

| Artifact | Source | Destination | Status |
|----------|--------|-------------|--------|
| [name] | [original path] | [template path] | Placed + Scored |

## Pipeline Phase Status

| Phase | Status | Score | Blocking Issues | Next Action |
|-------|--------|-------|-----------------|-------------|
| Discovery (Literature) | [Complete/Partial/Missing] | [XX/100 or --] | [count] | [what to do] |
| Discovery (Data) | [Complete/Partial/Missing] | [XX/100 or --] | [count] | [what to do] |
| Strategy | [Complete/Partial/Missing] | [XX/100 or --] | [count] | [what to do] |
| Execution (Code) | [Complete/Partial/Missing] | [XX/100 or --] | [count] | [what to do] |
| Execution (Paper) | [Complete/Partial/Missing] | [XX/100 or --] | [count] | [what to do] |
| Presentation | [Complete/Partial/Missing] | [XX/100 or --] | [count] | [what to do] |

## Quality Gate Assessment

- **Can advance to Strategy?** [Yes/No -- requires literature OR data assessment]
- **Can advance to Coding?** [Yes/No -- requires strategy score >= 80]
- **Can advance to Writing?** [Yes/No -- requires code score >= 80]
- **Can advance to Peer Review?** [Yes/No -- requires paper + code scores >= 80]

## Recommended Next Steps (Priority Order)

1. [Most impactful action -- e.g., "Fix 3 CRITICAL issues in Model/src/vfi.jl (type instability)"]
2. [Second priority -- e.g., "Add identification table to strategy memo (currently [TODO])"]
3. [Third priority]

## Convention Gaps (Non-Blocking)

These are template convention violations that do not affect correctness.
Address during the next review cycle, not now.

- [e.g., "Model code uses camelCase instead of snake_case (12 functions)"]
- [e.g., "No CairoMakie theme defined; figures use default colours"]
```

### Step 6: Save Onboarding Record

Save the full report to `quality_reports/session_logs/[date]_onboarding.md` for the research journal.

---

## Edge Cases

**User has only a paper draft, no code:**
Onboard the draft to Paper/. Extract model description from the paper into a strategy memo (even if incomplete). Report that Strategy is "partial" and Execution is "missing." The user can run `/identify` next to formalise the strategy, then `/data-analysis` to implement.

**User has code but no documentation:**
Onboard code to Model/ and scripts/. The Debugger will score it (likely low on comments and headers). Create a minimal strategy memo stub with `[TODO]` markers based on what the code appears to do. Report that Strategy is "stub" and needs the user's input.

**User has a working estimation pipeline:**
Onboard everything. If the code runs and produces results, the Debugger's strategic checks (categories 1-3) can verify code-strategy alignment. The main value of onboarding here is getting quality scores so the user knows where they stand before submission.

**User has files in non-standard formats (.m, .py, .docx):**
Copy to closest template location. Matlab goes to `scripts/matlab/`. Python to `scripts/python/`. Word documents go to `master_supporting_docs/` with a note to convert to LaTeX. Do not auto-convert.

---

## What This Skill Does NOT Do

- **Does not refactor code.** That is the Coder's job after the Debugger flags issues.
- **Does not rewrite prose.** That is the Writer's job after the Proofreader flags issues.
- **Does not run code.** That is the Verifier's job during compilation checks.
- **Does not create missing artifacts.** If the user has no strategy memo, this skill does not write one. It creates a stub with `[TODO]` markers and reports the gap. The user then runs `/identify` to fill it.
