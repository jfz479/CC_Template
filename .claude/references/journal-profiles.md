# Journal Profiles for Peer Review Calibration

**Loaded on demand by the Editor agent during `/review --peer [journal]`.**

---

## Economics — Top 5

### Econometrica
- **Scope:** Theoretical and empirical economics with mathematical rigor
- **Bar:** Novel methodology or deep structural insight; must advance frontier
- **Typical referee concerns:** Identification, functional form sensitivity, computational robustness
- **Structural papers:** Must have clear parameter-moment mapping, non-targeted moment fit
- **Turnaround:** 3–6 months
- **Desk rejection rate:** ~60%

### American Economic Review (AER)
- **Scope:** All fields of economics; emphasis on broad appeal
- **Bar:** Important question + credible answer; must interest non-specialists
- **Typical referee concerns:** External validity, policy relevance, robustness
- **Structural papers:** Clear counterfactual results, welfare implications
- **Turnaround:** 3–6 months
- **Desk rejection rate:** ~70%

### Review of Economic Studies (REStud)
- **Scope:** Theoretical and applied; values methodological innovation
- **Bar:** Methodological novelty or deep empirical insight
- **Typical referee concerns:** Mechanism clarity, model vs reduced-form comparison
- **Structural papers:** Why structural? What does it buy over reduced form?
- **Turnaround:** 4–8 months
- **Desk rejection rate:** ~65%

### Quarterly Journal of Economics (QJE)
- **Scope:** Empirical and applied; values big-picture questions
- **Bar:** Big question + compelling evidence; values natural experiments
- **Typical referee concerns:** Endogeneity, measurement, alternative explanations
- **Structural papers:** Less common; needs strong reduced-form motivation
- **Turnaround:** 3–6 months
- **Desk rejection rate:** ~75%

### Journal of Political Economy (JPE)
- **Scope:** Broad; traditionally strong in macro, labor, IO
- **Bar:** Important economic insight; tolerates longer, deeper papers
- **Typical referee concerns:** Model assumptions, data quality, generalizability
- **Structural papers:** Good fit; values well-identified structural estimation
- **Turnaround:** 4–8 months
- **Desk rejection rate:** ~65%

---

## Economics — Top Field (Macro-Labor)

### AEJ: Macroeconomics
- **Scope:** Macroeconomics with empirical or quantitative focus
- **Bar:** Clear contribution to macro understanding; well-calibrated models
- **Typical concerns:** Calibration targets, transition dynamics, welfare
- **Turnaround:** 3–5 months

### Journal of the European Economic Association (JEEA)
- **Scope:** All fields; European focus is a plus but not required
- **Bar:** Solid contribution; values institutional context
- **Typical concerns:** Cross-country validity, institutional detail
- **Turnaround:** 3–6 months

### Journal of Monetary Economics
- **Scope:** Monetary and macro; quantitative models welcome
- **Bar:** Clear policy relevance; well-solved models
- **Typical concerns:** Monetary transmission, steady-state vs transition
- **Turnaround:** 3–6 months

### International Economic Review (IER)
- **Scope:** International and macro; theoretical and empirical
- **Bar:** Solid technical contribution
- **Typical concerns:** Model complexity vs insight tradeoff
- **Turnaround:** 4–8 months

### Journal of Labor Economics (JoLE)
- **Scope:** Labor economics; empirical and structural
- **Bar:** Important labor market question + credible design
- **Typical concerns:** Selection, measurement of labor market outcomes
- **Turnaround:** 3–6 months

---

## Economics — Strong Field

### Labour Economics
- **Scope:** Applied labor; European labor markets
- **Bar:** Solid empirical or structural work; policy-relevant
- **Turnaround:** 2–4 months

### European Economic Review (EER)
- **Scope:** All fields; European relevance valued
- **Bar:** Competent empirical or theoretical work
- **Turnaround:** 3–5 months

### Macroeconomic Dynamics
- **Scope:** Dynamic macro models; DSGE, heterogeneous agents
- **Bar:** Well-solved quantitative models; computational contribution
- **Turnaround:** 3–6 months

### Journal of Economic Dynamics and Control (JEDC)
- **Scope:** Computational economics; dynamic models
- **Bar:** Methodological or computational innovation
- **Turnaround:** 3–6 months

### Quantitative Economics (QE)
- **Scope:** Quantitative methods in economics
- **Bar:** Novel econometric or computational methodology applied to economics
- **Turnaround:** 3–6 months

---

## Referee Dispositions

When simulating peer review, the Editor assigns each referee a **disposition** that shapes their focus:

| Disposition | Focus Area | Typical For |
|-------------|-----------|-------------|
| STRUCTURAL | Model specification, identification, computation | Econometrica, JPE, JME |
| CREDIBILITY | Causal design, robustness, threats to validity | AER, QJE, REStud |
| MEASUREMENT | Data quality, variable construction, sample selection | JoLE, Labour Economics |
| POLICY | Welfare implications, counterfactual relevance, external validity | AEJ:Macro, EER |
| THEORY | Theoretical foundations, mechanism clarity, model parsimony | Econometrica, REStud |
| COMPUTATIONAL | Numerical methods, convergence, solution accuracy | JEDC, QE, Macro Dynamics |

### Pet Peeves (Critical)
Referees with specific dispositions tend to flag:

**STRUCTURAL:** "Is this parameter identified? Show me the Jacobian." / "Why not a simpler model?" / "Non-targeted moment fit is poor."
**CREDIBILITY:** "What's the source of exogenous variation?" / "Parallel trends violated." / "Alternative explanation X."
**MEASUREMENT:** "This variable doesn't measure what you think." / "Sample selection concerns."
**POLICY:** "What's the welfare cost?" / "Transition dynamics matter." / "External validity beyond Germany?"
**COMPUTATIONAL:** "Convergence diagnostics?" / "Multiple starting points?" / "Grid sensitivity?"
