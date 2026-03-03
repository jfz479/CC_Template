---
name: structural-estimation
description: Guide structural estimation workflow. Covers model setup, moment computation, SMM objective, optimization (CMA-ES), diagnostics. Use when setting up or debugging estimation.
argument-hint: "[Model/ path or estimation question]"
allowed-tools: ["Read", "Grep", "Glob", "Write", "Edit", "Bash", "Task"]
---

# Structural Estimation Workflow

Guide for setting up and running SMM estimation in Julia.

## Step 1: Model Audit
Read Model/src/ files. Verify: types defined, VFI converges, simulation runs, moments computable.

## Step 2: Moment Selection
Read Data/moments/ for empirical targets.
Check: each estimated parameter has at least one disciplining moment.
Produce parameter-moment mapping table.

## Step 3: SMM Objective
Verify: objective = (m_data - m_model)' W (m_data - m_model)
Check weight matrix W choice (identity, diagonal, optimal).
Confirm simulation uses sufficient firms and periods.

## Step 4: Optimization
For CMA-ES or BlackBoxOptim: verify bounds, population size, max iterations.
Check: multiple random starting points used.
Confirm: results saved after each evaluation for crash recovery.

## Step 5: Diagnostics
After estimation: moment fit table, parameter sensitivity, non-targeted moments.
Generate: Tables/moment_fit.tex, Figures/moment_fit.pdf

## Step 6: Dispatch Agents
- Coder: implement any missing components
- Debugger: review Julia code quality
- Structural Modeler: audit identification and computation
