---
name: discover
description: Discovery phase skill with 4 modes — interview (formalize research idea), literature (structured lit review), data (dataset discovery), ideation (generate research questions). Replaces /interview-me, /lit-review, /find-data, /research-ideation.
disable-model-invocation: true
argument-hint: "[mode] [topic] — modes: interview, literature, data, ideation"
allowed-tools: ["Read", "Write", "Grep", "Glob", "WebSearch", "WebFetch", "Task"]
---

# Discover

Discovery phase skill with 4 modes. Parse the first word of `$ARGUMENTS` to select mode.

**Usage:**
- `/discover interview [topic]` — interactive research interview → spec + domain profile
- `/discover literature [topic]` — structured literature search + synthesis
- `/discover data [question]` — dataset discovery + quality assessment
- `/discover ideation [topic]` — generate research questions + strategies
- `/discover [topic]` — (no mode) defaults to `interview`

---

## Mode: interview

Conduct a structured interview to formalize a research idea into a concrete specification.

**Do NOT use AskUserQuestion.** Ask questions directly in text, one or two at a time.

### Interview Phases

1. **Big Picture (1-2 questions):** "What phenomenon or puzzle?" / "Why does this matter?"
2. **Theoretical Motivation (1-2):** "What's your intuition?" / "What would standard theory predict?"
3. **Data and Setting (1-2):** "What data do you have?" / "Specific context or time period?"
4. **Identification (1-2):** "Natural experiment or source of variation?" / "Biggest threat to causality?"
5. **Expected Results (1-2):** "What would you expect to find?" / "Policy or theory implications?"
6. **Contribution (1):** "How does this differ from what's been done?"

### After the Interview (5-8 exchanges)

**Output 1: Domain Profile** — if `.claude/references/domain-profile.md` has placeholders, fill it in. Otherwise confirm with user whether to update.

**Output 2: Research Specification** — save to `quality_reports/research_spec_[topic].md`:
- Research question, motivation, hypothesis, empirical strategy, data, expected results, contribution, open questions.

### Style
- Be curious, not prescriptive. Probe weak spots gently. Build on answers. Know when to stop.

---

## Mode: literature

Structured literature search dispatching **Librarian** (collector) and **Editor** (critic).

### Steps

1. **Context:** Read `.claude/references/domain-profile.md` for journals and seminal refs. Check `Bibliography_base.bib` for existing papers. Check `master_supporting_docs/`.

2. **Launch Librarian:**
   - Search: top-5 journals, field journals from domain-profile, NBER/SSRN/IZA, citation chains
   - Assign proximity scores (1-5): 1 = directly competes, 5 = tangentially related
   - Produce annotated bibliography with BibTeX entries
   - Save to `quality_reports/lit_review_[topic].md`

3. **Launch Editor (Lit Critic Mode):**
   - Check coverage gaps, journal quality distribution, scope, recency
   - Flag gaps for Librarian

4. **Fill gaps** if Editor identifies them (max 1 round)

5. **Present:** Summary, key papers with proximity/identification/findings, thematic organization, gaps, frontier, BibTeX entries

### Principles
- Prioritize published work over working papers. Note publication status.
- **Do NOT fabricate citations.** Flag uncertain details as `% UNVERIFIED`.
- Always note identification strategy and effect sizes.

---

## Mode: data

Dataset discovery dispatching **Explorer** (finder) and **Surveyor** (critic).

### Steps

1. **Context:** Read research spec and strategy memo if they exist. Read `.claude/references/domain-profile.md` for common data sources.

2. **Launch Explorer:**
   - Search: public microdata, administrative data, survey data, international, novel/alternative
   - For each: name, provider, access level, key variables, coverage, feasibility grade (A/B/C/D), strengths/limitations
   - Save to `quality_reports/data_exploration_[topic].md`

3. **Launch Surveyor (Data Critic):**
   - Check: measurement validity, sample selection, external validity, identification compatibility, known issues
   - Score each dataset. Flag deal-breakers.
   - Save to `quality_reports/data_critique_[topic].md`

4. **Synthesize:** Ranked recommendations, rejected datasets with reasons, data gaps, next steps

### Principles
- Explorer finds, Surveyor critiques. Never skip critique.
- Feasibility matters — a perfect dataset you can't access is useless.
- Measurement validity is key.

---

## Mode: ideation

Generate structured research questions, testable hypotheses, and empirical strategies.

### Steps

1. **Understand input.** Read any referenced files. Check `master_supporting_docs/` and `.claude/references/domain-profile.md`.

2. **Generate 3-5 research questions** ordered: descriptive → correlational → causal → mechanism → policy

3. **For each RQ:** hypothesis, identification strategy (method + treatment + control + key assumption), data requirements, potential pitfalls, related literature (2-3 papers)

4. **Rank** by feasibility and contribution

5. **Save** to `quality_reports/research_ideation_[topic].md`

### Principles
- Be creative but grounded. Every suggestion must be empirically feasible.
- Think like a referee — immediately identify the identification challenge.
- Suggest specific datasets where possible.
