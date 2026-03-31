# Session Logging & Reporting

Consolidates: session logging, session reporting, research journal.

---

## Session Logs

**Location:** `quality_reports/session_logs/YYYY-MM-DD_description.md`
**Template:** `templates/session-log.md`

### Three Triggers (all proactive)

1. **Post-Plan Log** — after plan approval: goal, approach, rationale, key context
2. **Incremental Logging** — append 1-3 lines whenever: a design decision is made, a problem is solved, the user corrects something, or the approach changes. Do not batch.
3. **End-of-Session Log** — when wrapping up: high-level summary, quality scores, open questions, blockers

---

## Session Report

**File:** `SESSION_REPORT.md` in project root
**Mirror:** `.claude/SESSION_REPORT.md` (kept in sync)

### Purpose

Single, append-only MD file that accumulates the full project history across sessions.

### Triggers

1. **End of every session** — proactively append before wrapping up
2. **On user request** — "update the report", "log what we did"
3. **After significant milestones** — commits, completed analyses, major decisions

### Entry Format

```markdown
## YYYY-MM-DD HH:MM — [Brief Title]

**Operations:**
- [Scripts run, files created/modified/deleted]

**Decisions:**
- [Choice made] — [rationale]

**Results:**
- [Key findings, outputs produced]
- [Errors encountered → how resolved]

**Commits:**
- `[hash]` [commit message]

**Status:**
- Done: [what's complete]
- Pending: [what remains]
```

### Rules

- **Append only** — never overwrite previous entries
- **Concise** — bullet points, 5-15 lines per entry
- **Include commit hashes** and **file paths** for modified files
- **Sync both copies** — root and `.claude/` must match
- **Do not duplicate** session logs — this is higher-level summary

---

## Research Journal

**File:** `quality_reports/research_journal.md`

### Purpose

Agent-level research history. Every agent invocation, phase transition, and escalation is logged here.

### Entry Format

```markdown
### YYYY-MM-DD HH:MM — [Agent Name]
**Phase:** [Discovery/Strategy/Execution/Peer Review/Presentation]
**Target:** [file or topic reviewed]
**Score:** [XX/100 or PASS/FAIL or N/A]
**Verdict:** [one line — the key finding or decision]
**Report:** [link to full report]
```

### What Gets Logged

| Event | Logged? |
|-------|---------|
| Agent review report | Yes — score + verdict |
| Phase transition | Yes — which phase, approval score |
| Escalation (three strikes) | Yes — strike count, escalation target |
| User override | Yes — what was overridden and why |
| R&R comment routing | Yes — classification + routing target |
| Score changes | Yes — before/after when resubmitted |

### Rules

- **Append only** — never edit previous entries
- **One entry per agent invocation**
- **Create the file** if it doesn't exist with header: `# Research Journal — [Project Name]`
