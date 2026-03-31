---
name: review
description: "Unified review skill with 7 modes: --paper (multi-agent excellence), --peer [journal] (simulated peer review), --proofread (manuscript polish), --code [file] (Julia/Stata review), --visual [file] (slide audit), --replicate (replication check), --all (everything). Replaces /paper-excellence, /review-paper, /proofread, /review-julia, /visual-audit."
disable-model-invocation: true
argument-hint: "[--mode] [file or 'all'] — modes: --paper, --peer, --proofread, --code, --visual, --replicate, --all"
allowed-tools: ["Read", "Grep", "Glob", "Write", "Task", "Bash"]
---

# Review

Unified review skill. Parse flags from `$ARGUMENTS` to select mode.

**Usage:**
- `/review --paper [file]` — multi-agent paper excellence review (weighted aggregate)
- `/review --peer [journal]` — simulated peer review with Domain + Methods Referees
- `/review --peer --stress [journal]` — adversarial stress test (harsh dispositions)
- `/review --proofread [file]` — 6-category manuscript proofreading
- `/review --code [file]` — Julia/Stata code review (Debugger standalone)
- `/review --visual [file]` — slide layout audit
- `/review --replicate [dir]` — replication package verification
- `/review --all` — runs --paper + --peer + --code in sequence

Default (no flag): `--paper Paper/main.tex`

---

## Mode: --paper (Paper Excellence)

Multi-agent review dispatching Structural Modeler, Debugger, Proofreader, and Verifier in parallel. Computes weighted aggregate score.

### Steps

1. **Identify targets:** If specific `.tex` file → review it. If `all` → `Paper/main.tex` + all scripts.
2. **Gather context:** Read `.claude/references/domain-profile.md`, `quality.md` weights, strategy memos.
3. **Launch 4 agents in parallel:**
   - **Structural Modeler** (25%): model class, identification, computation, economics
   - **Debugger** (15%): code quality categories 4-12 + strategic alignment 1-3 if strategy memo exists
   - **Proofreader** (25% paper + 10% polish): 6 check categories
   - **Verifier** (5%): LaTeX compilation, script execution, file integrity, output freshness
4. **Talk review** (advisory, if `Talks/*.tex` exist): notation match, slide count
5. **Compute weighted aggregate** per `quality.md`. Renormalize if components missing.
6. **Present unified report** with score breakdown, priority fixes, gate status.
7. **Gate enforcement:** >= 95 submission-ready, >= 90 PR-ready, >= 80 commit-ready, < 80 blocked.

---

## Mode: --peer (Simulated Peer Review)

Dispatches **Domain Referee** + **Methods Referee** (blind, independent) and **Editor** for editorial decision.

### Steps

1. **Context:** Read paper, `.claude/references/domain-profile.md`, `Bibliography_base.bib`, strategy memo.
2. **Journal calibration:** If `[journal]` specified, read `.claude/references/journal-profiles.md` for:
   - Journal scope, bar, typical concerns
   - Assign referee dispositions matching journal (e.g., Econometrica → STRUCTURAL + COMPUTATIONAL)
3. **Launch 2 Referees in parallel:**
   - **Domain Referee** (subagent_type: domain-referee): contribution, literature, data, institutional context, policy relevance
   - **Methods Referee** (subagent_type: methods-referee): identification, model specification, computation, robustness, replication
   - Save reports to `quality_reports/domain_referee_report.md` and `quality_reports/methods_referee_report.md`
4. **Launch Editor:** Read both reports, synthesize decision (Accept / Minor / Major / Reject), identify mandatory vs optional revisions. Save to `quality_reports/editorial_decision.md`.
5. **Present:** Editorial decision, referee summaries, mandatory revisions, full report links.

### --stress flag

Adversarial stress test mode:
- Assign adversarial dispositions (SKEPTIC for both referees)
- Maximum severity calibration
- Tests whether paper survives hostile but fair review

### --peer --r2 flag (R2 round)

For R&R second rounds:
- Referees read their own previous reports (from `quality_reports/`)
- Check whether previous concerns were adequately addressed
- Score improvement relative to first round

---

## Mode: --proofread (Manuscript Polish)

Dispatches **Proofreader** agent.

### Steps

1. **Identify files:** Specific `.tex` file, or `all` for `Paper/main.tex` + `Paper/sections/`. Talks are advisory.
2. **Launch Proofreader** with 6 categories:
   - Structure — contribution in first 2 pages, standard sequence
   - Claims-Evidence — numbers match tables, effect sizes with units
   - Identification Fidelity — paper matches strategy memo
   - Writing Quality — anti-hedging, notation consistency
   - Grammar & Polish — agreement, articles, tense
   - Compilation & LaTeX — overfull hbox, undefined refs/cites
3. **Scoring:** Deductions per `quality.md`. Paper = blocking, talks = advisory.
4. **Present:** Issues by category, score, top 3 critical issues.

**IMPORTANT:** Do NOT edit source files. Report only.

---

## Mode: --code (Code Review)

Dispatches **Debugger** agent in standalone mode (categories 4-12 only).

### Steps

1. **Identify files:** Specific `.jl` or `.do` file, or scan `Model/src/`, `scripts/julia/`, `scripts/stata/`.
2. **Read `.claude/rules/julia-code-conventions.md`** (for Julia files) or Stata conventions.
3. **Launch Debugger** (standalone, categories 4-12):
   - Structure, output, reproducibility, types, figures, saves, comments, guards, polish
   - If strategy memo exists, add categories 1-3 (strategic alignment)
4. **Scoring:** Per `quality.md` Julia/Stata rubrics. Performance issues = Critical.
5. **Present:** Issues by severity, score, recommendations.

**IMPORTANT:** Do NOT edit source files. Report only.

---

## Mode: --visual (Slide Audit)

Adversarial visual audit of Beamer or Quarto slides.

### Steps

1. Read the slide file and check for:
   - Overflow (content exceeding slide boundaries)
   - Font consistency (mixed sizes, unexpected changes)
   - Box fatigue (too many colored boxes)
   - Spacing issues (cramped or wasted space)
   - Semantic color usage
2. **Spacing-first principle:** reduce vertical margins → consolidate lists → move equations inline → reduce image size → font reduction as last resort.
3. **Present:** Issues by severity, suggested fixes.

---

## Mode: --replicate (Replication Check)

Dispatches **Verifier** agent in submission mode (all 10 checks).

### Steps

1. **Launch Verifier** with 10 checks:
   - LaTeX compilation, script execution, file integrity, output freshness
   - Package inventory, dependency documentation, data provenance
   - End-to-end execution, output cross-reference, README completeness (AEA format)
2. **Present:** Pass/fail per check, blocking issues, recommendations.

---

## Mode: --all (Everything)

Runs `--paper` then `--peer` then `--code` sequentially. Produces a combined report with all scores.

---

## Principles

- **Paper scores are blocking.** Must pass quality gates.
- **Talk scores are advisory.** Reported but non-blocking.
- **Parallel execution** wherever possible.
- **Don't double-count.** Same issue in multiple reports counts once.
- **One unified report.** User sees one priority list.
