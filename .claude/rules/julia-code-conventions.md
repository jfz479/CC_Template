---
paths:
  - "**/*.jl"
  - "Model/**/*.jl"
  - "scripts/**/*.jl"
---

# Julia Code Standards

**Standard:** High-performance scientific computing for structural estimation

## 1. Project Structure
- `Project.toml` + `Manifest.toml` for reproducibility
- Activate: `using Pkg; Pkg.activate(".")` at entry points
- Relative paths or `@__DIR__`; `mkpath()` for output dirs

## 2. Type System
- `@kwdef struct` for parameters; concrete types in fields (never `Any`)
- Separate calibrated from estimated params in distinct structs
- Document units and source above each field

## 3. Performance
- Type stability: use `@code_warntype` to verify
- Avoid closures in hot loops; pass data explicitly
- Pre-allocate output arrays; use `@view`/`@views` for slicing
- `@.` for broadcasting; `SparseArrays` for transition matrices
- `Threads.@threads` for parallel loops; `Distributed.jl` for CMA-ES
- Profile first: `@time`, `@btime`, `@profile`

## 4. Naming
- `snake_case` functions/variables; `PascalCase` types/modules
- Verb-noun: `compute_value!`, `simulate_panel`, `build_grid`
- `!` suffix for mutating functions; `UPPER_SNAKE_CASE` constants

## 5. Value Function Iteration
- Pre-allocated arrays for value/policy functions
- Backward induction where possible
- Convergence: `maximum(abs.(V_new .- V_old)) < tol`
- Report iteration count; guard against NaN/Inf

## 6. SMM Estimation
- Objective returns scalar loss (weighted distance)
- Document moment definitions: population vs sample analog
- `Random.seed!(seed)` for simulation reproducibility
- Report: estimates, SEs, moment fit table

## 7. Plotting (Makie)
- `CairoMakie` for PDF/SVG; `GLMakie` for interactive only
- Paper: `Figure(size = (800, 500))`
- Beamer: `Figure(size = (1000, 400))`, `backgroundcolor = :transparent`
- Save: `save("Figures/name.pdf", fig)`

## 8. Tables
- `PrettyTables.jl` with `tf_latex_booktabs`
- Save bare tabular to `Tables/`; wrapper in `main.tex`

## 9. Model/ Layout
```
Model/src/  types.jl | setup.jl | vfi.jl | simulation.jl | moments.jl | estimation.jl | helpers.jl
Model/scripts/  run_estimation.jl | run_simulation.jl | run_counterfactual.jl
```

## 10. Prohibited Patterns
- Global mutable state in hot paths
- `Any` typed containers or untyped struct fields
- Growing arrays in VFI loops (pre-allocate)
- Hardcoded absolute paths
- `eval`/`include` inside functions
