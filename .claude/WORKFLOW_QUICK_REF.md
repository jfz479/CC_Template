# Workflow Quick Reference

**Model:** Contractor (you direct, Claude orchestrates via dependency graph)

---

## The Research Pipeline

```
/discover interview → Research Spec + Domain Profile
    ↓
/discover literature → Literature Synthesis (Librarian + Editor)
    ↓
/discover data → Data Assessment (Explorer + Surveyor)
    ↓
/identify → Strategy Memo (Strategist + Structural Modeler)
    ↓
/data-analysis → Scripts + Output (Coder + Debugger)
    ↓
/draft-paper → Paper Sections (Writer + Humanizer)
    ↓
/review --paper → Weighted Score (4 agents parallel)
    ↓
/review --peer [journal] → Peer Review (Domain + Methods Referee + Editor)
    ↓
/submit → Final Gate (score >= 95, all components >= 80)
```

Enter at any stage. Use `/new-project` for the full pipeline. Use `/onboard` if the user points to new onboarding_material.

---

## Key Skills by Research Stage

### Discovery
| Command | Agents | What It Does |
|---------|--------|-------------|
| `/discover interview [topic]` | — | Interactive Q&A → research spec + domain profile |
| `/discover literature [topic]` | Librarian + Editor | Literature search + synthesis |
| `/discover data [question]` | Explorer + Surveyor | Data discovery + quality assessment |
| `/discover ideation [topic]` | — | Research questions + strategies |

### Strategy
| Command | Agents | What It Does |
|---------|--------|-------------|
| `/identify [question]` | Strategist + Structural Modeler | Design model + estimation strategy |
| `/structural-estimation` | — | SMM estimation workflow guidance |

### Execution
| Command | Agents | What It Does |
|---------|--------|-------------|
| `/data-analysis [dataset]` | Coder + Debugger | End-to-end analysis + code review |
| `/draft-paper [section]` | Writer | Paper sections + humanizer pass |
| `/tools compile [file]` | — | Paper: latexmk; Talks: 3-pass XeLaTeX + bibtex |

### Quality & Review
| Command | Agents | What It Does |
|---------|--------|-------------|
| `/review --paper [file]` | 4 parallel | Multi-agent review + weighted score |
| `/review --peer [journal]` | Domain + Methods Referee + Editor | Journal-calibrated peer review |
| `/review --proofread [file]` | Proofreader | 6-category manuscript review |
| `/review --code [file]` | Debugger | Julia/Stata code quality (standalone) |
| `/review --visual [file]` | — | Slide layout audit |
| `/tools validate-bib` | — | Cross-reference citations |

### Submission & Deposit
| Command | Agents | What It Does |
|---------|--------|-------------|
| `/target-journal [paper]` | Editor | Journal targeting + strategy |
| `/respond-to-referee [report]` | Writer + routing | Point-by-point response |
| `/data-deposit` | Coder + Verifier | AEA replication package |
| `/review --replicate [dir]` | Verifier | Replication package check |
| `/submit [journal]` | Verifier + scoring | Final gate (score >= 95) |

### Presentations
| Command | Agents | What It Does |
|---------|--------|-------------|
| `/create-talk [format]` | Storyteller + Discussant | Beamer talk (4 formats) |
| `/review --visual [file]` | — | Slide layout audit |

### Infrastructure
| Command | What It Does |
|---------|-------------|
| `/tools commit [msg]` | Stage, commit, PR, merge |
| `/humanizer [file]` | Strip 24 AI writing patterns |
| `/journal` | Research journal timeline |
| `/context-status` | Session health + context usage |
| `/learn` | Extract discoveries into skills |
| `/deploy` | Build + deploy to GitHub Pages |

---

## Quality Gates

| Score | Gate | What It Means |
|-------|------|--------------|
| >= 95 | Submission | Ready for top-5 (all components >= 80) |
| >= 90 | PR | Ready to submit (minor polish recommended) |
| >= 80 | Commit | Ready to commit (address major issues before submission) |
| < 80 | **Blocked** | Must fix critical/major issues |
| >= 60 | Exploration | Acceptable for `explorations/` work |
| -- | Advisory | Talks: reported only, non-blocking |

Weighted aggregate: Literature 10% + Data 10% + Identification 25% + Code 15% + Paper 25% + Polish 10% + Replication 5%

---

## On-Demand References

These files live in `.claude/references/` and load only when agents need them (not every session):

| File | Loaded By | Content |
|------|-----------|---------|
| `domain-profile.md` | Librarian, Editor, Referees | Field, journals, data, conventions |
| `journal-profiles.md` | Editor, Referees | 20 journal profiles + referee dispositions |
| `table-standards.md` | Coder, Writer | Publication-quality table formatting |
| `working-paper-format.md` | Writer, Proofreader | Paper.sty conventions codified |

---

## I Ask You When

- **Design forks:** "Option A vs. Option B. Which?"
- **Solution algorithm choice:** "VFI, PFI, Collocation or different method for this setting?"
- **Disagreement with referee:** "DISAGREE classification — please review"
- **After 3 strikes:** "Coder and Debugger can't agree — your call"

## I Just Execute When

- Code fix is obvious (bug, pattern)
- Verification (compilation, tolerance checks)
- Documentation (logs, commits)
- Plotting (per established standards)

---

## Exploration Mode

For experimental work:
- Work in `explorations/` folder
- 60/100 quality threshold (vs. 80/100 for production)
- No plan needed — just a research value check
- See `.claude/rules/exploration-fast-track.md`

---

## Next Step

You provide task → I plan (if needed) → Your approval → Execute → Done.
