# Agent Governance

Consolidates: adversarial pairing, separation of powers, three-strikes escalation.

---

## Adversarial Pairing (Rule 1)

**Every worker agent has a paired critic. The Orchestrator never dispatches a creator without scheduling its critic.**

| Worker (Creator) | Critic (Reviewer) | What's Reviewed |
|-----------------|-------------------|-----------------|
| Librarian | Editor | Literature coverage, gaps, recency |
| Explorer | Surveyor | Data feasibility, quality, identification fit |
| Strategist | Structural Modeler | Model design, identification, computation |
| Coder | Debugger | Code quality, reproducibility, code-strategy alignment |
| Writer | Proofreader | Manuscript polish, LaTeX quality, hedging |
| Storyteller | Discussant | Talk structure, audience calibration, visual quality |

### Peer Review (Special Case)

Peer Review uses a different structure — the Editor dispatches two independent Referees:

1. Editor assigns the paper to a **Domain Referee** and a **Methods Referee** (blind, independent)
2. Both Referees produce scored reports with different foci
3. Editor synthesizes a decision: Accept / Minor Revisions / Major Revisions / Reject

### Enforcement

- If a creator artifact exists without a critic score, it is **not approved**
- No artifact advances to the next phase without its critic's score >= 80
- Critics produce scores; creators produce artifacts — never the reverse

---

## Separation of Powers (Rules 5 & 6)

### Rule 5: Critics Never Create

**What critics DO:** Score artifacts against a rubric, list issues with severity/deductions, suggest fixes as recommendations.

**What critics DON'T DO:** Write code, rewrite paper sections, produce alternative implementations.

**Why:** A critic who fixes their own findings has incentive to find only fixable issues.

### Rule 6: Creators Can't Self-Score

| Agent | Creates | Scored By |
|-------|---------|-----------|
| Librarian | Annotated bibliography | Editor |
| Explorer | Data assessment | Surveyor |
| Strategist | Strategy memo | Structural Modeler |
| Coder | Julia/Stata scripts | Debugger |
| Writer | Paper manuscript | Proofreader |
| Storyteller | Beamer talk | Discussant |

The Orchestrator flags violations: if a critic produces files or a creator reports its own score → discard, re-dispatch.

---

## Three Strikes Escalation (Rule 7)

**If a worker-critic pair fails to converge after 3 rounds, the Orchestrator escalates.**

```
Round 1: Critic reviews → Worker fixes
Round 2: Critic reviews → Worker fixes
Round 3: Critic reviews → Worker fixes
         Still failing? → ESCALATION
```

### Escalation Routing

| Pair | Escalation Target | What Happens |
|------|-------------------|--------------|
| Coder + Debugger | Structural Modeler | Re-evaluates whether strategy memo is implementable |
| Writer + Proofreader | Editor | Structural rewrite, not just polish |
| Strategist + Structural Modeler | User | Fundamental design question — needs human judgment |
| Librarian + Editor | User | Scope disagreement — user decides breadth vs depth |
| Explorer + Surveyor | User | Data feasibility deadlock — user decides resource trade-offs |
| Storyteller + Discussant | User | Talk scope/format disagreement |

### Rules

- **Max 3 rounds per pair per invocation** — no infinite loops
- **Escalation is logged** in the research journal with strike count
- **User escalation requires a clear question** — not "they disagree," but "X requires Y, which contradicts Z. Which takes priority?"
- **Post-escalation:** Worker starts fresh from the escalation target's decision
