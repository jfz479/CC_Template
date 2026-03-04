---
name: identify
description: Design model design strategy by dispatching the Strategist agent (proposer) and structural-modeler agent (critic). Produces strategy memo, pseudo-code, robustness plan, and falsification tests. Use when asked to "design the strategy" or "write the strategy memo".
argument-hint: "[research question or research-spec file path]"
allowed-tools: ["Read", "Grep", "Glob", "Write", "Task"]
---

# Identify

Design an identification strategy by dispatching the **Strategist** (proposer) and **structural-modeler** (critic).

**Input:** `$ARGUMENTS` — research question, description of available variation, or path to research spec.

---

## Workflow

### Step 1: Context Gathering

1. Read research spec if exists (`quality_reports/research_spec_*.md`)
2. Read literature review if exists (`quality_reports/lit_review_*.md`)
3. Read data assessment if exists (`quality_reports/data_exploration_*.md`)
4. Read `.claude/rules/domain-profile.md` for common identification strategies in the field

### Step 2: Launch Strategist Agent

Delegate to the `strategist` agent via Task tool:

```
Prompt: Design identification strategy for "[research question]".
Available data: [from context gathering].
Available variation: [from research spec or user description].

Produce:
  1. Strategy memo — design choice, estimand, assumptions, comparison group
  2. Pseudo-code — implementation sketch (what the Julia/Stata/R/Matlab code will do)
  3. Robustness plan — ordered list of robustness checks with rationale
  4. Falsification tests — what SHOULD NOT show effects (and why)
  5. Referee objection anticipation — top 5 objections with pre-emptive responses

If multiple credible strategies exist, present the top 2-3 with trade-offs.
Save strategy memo to quality_reports/strategy_memo_[topic].md
```

### Step 3: Launch structural-modeler Agent (Strategy Review Mode)

After Strategist returns, delegate to the `structural-modeler` agent:

```
Prompt: Review the strategy memo at quality_reports/strategy_memo_[topic].md.
Mode: Strategy review (Phase 2 focus -- identification).
Check:
  - Does the model contain all ingredients to answer the research question?
  - Are some modelling aspects too simple or too complicated?
  - Is the model likely solvable in an appropriate amount of time?
  - Does the data exist to discipline the moment?
  - Does each parameter have a disciplining moment?
  - Are normalisations resolved?
  - Are functional forms defended?
  - Are the biggest threats acknowledged?
  - Does the counterfactual design cleanly isolate the mechanism?
Score the strategy. Flag CRITICAL issues that must be resolved before coding.
Save review to quality_reports/strategy_memo_[topic]_model_review.md
```

### Step 4: Iterate if Needed

If structural-modeler finds CRITICAL issues:
1. Present issues to user
2. Re-dispatch Strategist with specific fixes (max 3 rounds per three-strikes.md)
3. Re-run structural-modeler to verify

If unresolved after 3 rounds: escalate to user with both perspectives.

### Step 5: Present Results

```markdown
# Model Strategy: [Topic]
**Date:** [YYYY-MM-DD]
**Model class:** [Search-and-matching / Firm dynamics / HA / ...]
**Estimation method:** [SMM / GMM / Calibration / None].

## Strategy Summary
[2-3 sentence description of the model strategy]

## Key Assumptions
1. [Assumption 1] — [how it will be defended]
2. [Assumption 2] — [how it will be defended]

## structural-modeler Assessment: [SOUND / CONCERNS / CRITICAL ISSUES]
- Critical issues: N
- Major issues: N
- Strategy memo score: XX/100

## Robustness Plan (ordered)
1. [Most important check]
2. [Second check]
...

## Next Steps
- [ ] [PROPOSE NEXT STEPS]
```

---

## Principles

- **Strategist proposes, structural-modeler critiques.** The adversarial pairing catches design flaws early.
- **Strategy memo is the contract.** Once approved, the Coder implements it faithfully.
- **Catch problems before coding.** A flawed strategy caught now saves weeks of wasted analysis.
- **Multiple strategies are OK.** Present trade-offs and let the user choose.
- **The user decides.** If Strategist and structural-modeler disagree after 3 rounds, the user resolves it.
