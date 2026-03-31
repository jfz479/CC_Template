# Quality Gates & Scoring

Consolidates: scoring protocol, severity gradient, quality gates, and deduction rubrics.

---

## Gate Thresholds

| Score | Gate | What It Means |
|-------|------|--------------|
| >= 95 | Submission | Ready for top-5 (all components >= 80) |
| >= 90 | PR | Ready to submit (minor polish recommended) |
| >= 80 | Commit | Good enough to save |
| < 80 | **Blocked** | Must fix critical/major issues |
| — | Advisory | Talks: reported only, non-blocking |
| >= 60 | Exploration | Acceptable for `explorations/` work |

User can override any gate with justification.

---

## Weighted Aggregation

| Component | Weight | Source Agent |
|-----------|--------|-------------|
| Literature coverage | 10% | Editor's score of Librarian |
| Data quality | 10% | Surveyor's score of Explorer |
| Model & identification | 25% | Structural Modeler's score of Strategist |
| Code quality | 15% | Debugger's score of Coder |
| Paper quality | 25% | Average of Domain Referee + Methods Referee |
| Manuscript polish | 10% | Proofreader's score of Writer |
| Replication readiness | 5% | Verifier pass/fail (0 or 100) |

**Per-component minimum:** No component below 80 for submission.

**Missing components:** Excluded from average; remaining weights renormalized.

---

## Severity Gradient

**Critics calibrate severity based on the phase of the project.**

| Phase | Critic Stance | Rationale |
|-------|--------------|-----------|
| Discovery | Encouraging (low) | Early ideas need space |
| Strategy | Constructive (medium) | Identification must be sound |
| Execution | Strict (high) | Code and paper are near-final |
| Peer Review | Adversarial (maximum) | Simulates real referees |
| Presentation | Professional (medium-high) | Polished but advisory |

The same issue may have different deductions by phase:

| Issue | Discovery | Strategy | Execution | Peer Review |
|-------|-----------|----------|-----------|-------------|
| Missing citation | -2 | -5 | -10 | -15 |
| Notation inconsistency | -1 | -3 | -5 | -5 |
| Hedging language | — | — | -3 | -5 |
| Missing robustness check | — | -5 | -15 | -20 |

---

## Deduction Rubrics

### Paper LaTeX (.tex in Paper/)

| Severity | Issue | Deduction |
|----------|-------|-----------|
| Critical | Compilation failure | -100 |
| Critical | Numbers in text don't match tables | -25 |
| Critical | Undefined citation | -15 |
| Critical | Broken reference (\ref) | -15 |
| Critical | Overfull hbox > 10pt | -10 |
| Critical | Typo in equation | -10 |
| Major | Notation inconsistency | -5 |
| Major | Missing figure/table at referenced path | -5 |
| Major | Hedging language | -3 per (max -15) |
| Minor | Overfull hbox 1-10pt | -1 |

### Julia Scripts (.jl)

| Severity | Issue | Deduction |
|----------|-------|-----------|
| Critical | Script does not run / module fails to load | -100 |
| Critical | Type instability in VFI inner loop | -30 |
| Critical | VFI does not converge (no convergence check) | -25 |
| Critical | Hardcoded absolute paths | -20 |
| Critical | NaN/Inf not guarded in log/division | -20 |
| Major | No Random.seed! in simulation | -15 |
| Major | Growing arrays inside VFI loop | -15 |
| Major | Untyped struct fields | -10 |
| Major | Missing JLD2 save for results | -10 |
| Major | Figures/Tables not saved to correct dirs | -5 |
| Minor | No documentation header | -5 |
| Minor | Inconsistent naming | -3 |
| Minor | Dead code | -2 |

### R Scripts (.R)

| Severity | Issue | Deduction |
|----------|-------|-----------|
| Critical | Script doesn't run | -100 |
| Critical | Domain-specific bugs | -30 |
| Critical | Code doesn't match strategy memo | -25 |
| Critical | Hardcoded absolute paths | -20 |
| Major | Missing robustness checks | -15 |
| Major | Wrong clustering level | -15 |
| Major | Missing set.seed() | -10 |
| Major | Missing RDS saves | -10 |
| Major | Implausible result magnitude | -10 |
| Minor | No documentation headers | -5 |

### Stata Scripts (.do)

| Severity | Issue | Deduction |
|----------|-------|-----------|
| Critical | Script doesn't run | -100 |
| Critical | Domain-specific bugs | -30 |
| Critical | Code doesn't match strategy memo | -25 |
| Critical | Hardcoded absolute paths | -20 |
| Major | Missing robustness checks | -15 |
| Major | Missing `set seed` | -10 |
| Major | Missing `esttab`/`outreg2` output | -5 |
| Minor | No documentation headers | -5 |

### Talks (Advisory Only — Non-Blocking)

| Severity | Issue | Deduction |
|----------|-------|-----------|
| Critical | XeLaTeX compilation failure | -100 |
| Major | Slide count outside format range | -10 |
| Major | Result not in paper | -10 |
| Major | Notation mismatch with paper | -5 |
| Minor | Overfull hbox | -2 |

---

## Quality Reports

Generated **only at merge time**. Use `templates/quality-report.md`.
Save to `quality_reports/merges/YYYY-MM-DD_[branch-name].md`.
