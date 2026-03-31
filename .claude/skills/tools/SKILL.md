---
name: tools
description: "Infrastructure utilities with sub-commands: compile (LaTeX/XeLaTeX), validate-bib (citation cross-reference), commit (stage/commit/PR/merge). Replaces /compile-latex, /validate-bib, /commit."
disable-model-invocation: true
argument-hint: "[sub-command] [args] — sub-commands: compile, validate-bib, commit"
allowed-tools: ["Read", "Write", "Grep", "Glob", "Bash"]
---

# Tools

Infrastructure utilities. Parse the first word of `$ARGUMENTS` to select sub-command.

**Usage:**
- `/tools compile [file]` — compile LaTeX (paper: latexmk, talks: 3-pass XeLaTeX + bibtex)
- `/tools validate-bib` — cross-reference all citations against bibliography
- `/tools commit [message]` — stage, commit, create PR, merge

---

## Sub-command: compile

Compile LaTeX with format-specific settings.

### Paper (.tex in Paper/)

```bash
cd Paper && latexmk -pdf main.tex
```

Post-compile checks:
- Scan log for overfull hbox warnings (flag > 10pt)
- Check for undefined citations (`Citation .* undefined`)
- Check for undefined references (`Reference .* undefined`)
- Open PDF for visual check if on macOS: `open main.pdf`

### Talks (.tex in Talks/)

```bash
cd Talks
TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode $FILE.tex
BIBINPUTS=..:$BIBINPUTS bibtex $FILE
TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode $FILE.tex
TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode $FILE.tex
```

Post-compile: same checks as paper.

### Auto-detect

If no file specified:
- If in `Paper/`: compile `main.tex` with latexmk
- If in `Talks/`: compile all `.tex` files with XeLaTeX
- Otherwise: ask which file to compile

---

## Sub-command: validate-bib

Cross-reference all citations in `.tex` and `.qmd` files against bibliography entries.

### Steps

1. Scan all `.tex` files in `Paper/` and `Talks/` for `\cite`, `\citet`, `\citep`, `\citeauthor`, `\citeyear` commands. Extract citation keys.
2. Read `Bibliography_base.bib` and extract all entry keys.
3. Report:
   - **Missing entries:** cited in .tex but not in .bib
   - **Unused entries:** in .bib but never cited (informational, not errors)
   - **Potential typos:** near-matches between cited keys and bib keys
   - **Quality issues:** entries missing required fields (year, author, title)
4. Return counts: `X citations found, Y missing, Z unused, W quality issues`

**Read-only** — does not modify any files.

---

## Sub-command: commit

Standard commit-PR-merge cycle.

### Steps

1. **Pre-flight:** `git status`, `git diff --stat` — show what will be committed
2. **Create branch** (if on main): `git checkout -b [descriptive-branch-name]`
3. **Stage files:** Add relevant files (exclude `.env`, credentials, large binaries)
4. **Commit:** Use message from `$ARGUMENTS` or auto-generate from changes
5. **Push:** `git push -u origin [branch]`
6. **Create PR:** `gh pr create --title "[message]" --body "[auto-summary]"`
7. **Merge:** `gh pr merge --merge`
8. **Cleanup:** `git checkout main && git pull`

### Commit message format

```
[type]: [description]

[optional body with details]

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```

Types: `feat`, `fix`, `refactor`, `docs`, `style`, `test`, `chore`

### Safety

- Never commit files matching: `.env`, `*credentials*`, `*secret*`, `*.key`
- Warn before force-pushing to main
- Show diff summary before committing — wait for user confirmation
