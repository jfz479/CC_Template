# Domain Profile

## Field

**Primary:** Macro-Labor Economics (structural)
**Adjacent subfields:** Search-and-matching theory, firm dynamics, labor market policy evaluation, quantitative macroeconomics

---

## Target Journals (ranked by tier)

| Tier | Journals |
|------|----------|
| Top-5 | Econometrica, AER, REStud, QJE, JPE |
| Top field | AEJ:Macro, JEEA, Journal of Monetary Economics, IER, JoLE |
| Strong field | Labour Economics, EER, Macroeconomic Dynamics, JEDC, QE |
| Specialty | German Economic Review, IZA Journal of Labor Economics |

---

## Common Data Sources

| Dataset | Type | Access | Notes |
|---------|------|--------|-------|
| IAB Establishment Panel | Panel survey | Restricted (IAB) | Employment, wages, STW; 2011-2019 |
| SIAB | Admin panel | Restricted (IAB) | Wage histories; censored at ceiling |
| Mikrozensus | Survey | Restricted (DESTATIS) | Education, search behavior |
| PKuG-EAK / IEB | Admin | Restricted (IAB) | STW spells, employment biographies |

---

## Common Identification Strategies

| Strategy | Application | Key Assumption |
|----------|------------|----------------|
| Structural estimation (SMM) | Firm dynamics, search models | Model specification, moment selection |
| Calibration + counterfactual | Policy evaluation (STW) | Model captures relevant adjustment margins |
| Value function iteration | Dynamic choice problems | Convergence, grid resolution |

---

## Field Conventions

- Structural models require explicit identification discussion: which moments pin down which parameters
- Goodness-of-fit tables: targeted vs non-targeted moments
- Policy counterfactuals should include transition dynamics, not just steady states
- Welfare analysis expected: compensating variation or consumption equivalents
- Computational details in online appendix: grids, convergence, algorithms
- German institutional context must be documented: collective bargaining, working-time accounts, STW rules
- For search models: report matching elasticity, v/u ratio, job-finding and separation rates

---

## Notation Conventions

| Symbol | Meaning | Anti-pattern |
|--------|---------|-------------|
| $z$ | Idiosyncratic productivity (AR(1)) | Not $a$ or $\epsilon$ |
| $X$ | Permanent type | Normalize $E[X]=1$ |
| $n$ | Employment (FTE) | FTE not headcount |
| $h$ | Hours per worker, $h \in [0,1]$ | |
| $\eta$ | Bargaining power | Not $\beta$ (discount factor) |
| $\delta$ | Separation rate | Distinguish exogenous vs endogenous |
| $\theta$ | Market tightness ($v/u$) | Equilibrium object |

---

## Seminal References

| Paper | Why It Matters |
|-------|---------------|
| Cooper, Haltiwanger, Willis (2007/2017) | Firm dynamics with non-convex adjustment costs |
| Elsby and Michaels (2013) | Search-and-matching with heterogeneous firms |
| Hopenhayn (1992) | Canonical firm dynamics entry/exit |
| Stole and Zwiebel (1996) | Intrafirm bargaining |
| Clementi and Palazzo (2016) | Random fixed costs for exit |
| Menzio and Shi (2011) | Block recursive equilibrium, directed search |

---

## Field-Specific Referee Concerns

- "Is the model identified?" -- Jacobian or sensitivity analysis for parameter-moment mapping
- "Why not a simpler model?" -- justify each ingredient with data patterns
- "Sensitivity to functional form?" -- matching function, productivity process, costs
- "General equilibrium effects?" -- partial vs general equilibrium for counterfactuals
- "Computational robustness" -- multiple starting points, convergence diagnostics
- "External validity of German institutions" -- transferability outside Germany
- "Why these moments?" -- moment selection must be motivated
- "Non-targeted moment fit" -- over-identification or at minimum reporting

---

## Quality Tolerance Thresholds

| Quantity | Tolerance | Rationale |
|----------|-----------|-----------|
| VFI convergence | 1e-8 | Standard |
| Moment fit | < 10% deviation | Targeted moments |
| Simulation SE | Report across seeds | Monte Carlo variation |
