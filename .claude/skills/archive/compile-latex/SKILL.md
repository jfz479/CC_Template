---
name: compile-latex
description: Compile LaTeX. Paper uses latexmk -pdf. Talks use 3-pass XeLaTeX + bibtex.
argument-hint: "[filename without .tex, or 'paper']"
allowed-tools: ["Read", "Bash", "Glob"]
---

# Compile LaTeX

## For Paper (Paper/main.tex):
```bash
cd Paper && latexmk -pdf -interaction=nonstopmode main.tex
```

## For Talks (Talks/*.tex):
```bash
cd Talks
TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode $ARGUMENTS.tex
BIBINPUTS=..:$BIBINPUTS bibtex $ARGUMENTS
TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode $ARGUMENTS.tex
TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode $ARGUMENTS.tex
```

## Detection
If $ARGUMENTS is "paper" or "main", compile Paper/main.tex with latexmk.
If $ARGUMENTS matches a file in Talks/, compile with XeLaTeX 3-pass.
Otherwise ask the user.

## Post-compile checks
- Grep for Overfull hbox warnings
- Grep for undefined citations
- Report page count
- Open PDF for visual check
