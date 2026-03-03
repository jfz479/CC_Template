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
- Type stability: use `@code_warntype` to verify hot paths
- Avoid closures in hot loops; pass data explicitly as arguments
- Pre-allocate output arrays; use `@view`/`@views` for slicing
- `@.` for broadcasting; `SparseArrays` for transition matrices
- Profile first: `@time`, `@btime`, `@profile`

### Parallelism: Threads vs Distributed

Two parallelism models exist in Julia. They solve different problems. Choosing the wrong one costs either correctness (data races) or performance (unnecessary serialisation). The decision depends on whether parallel tasks share mutable state.

**Threads.jl (shared memory, single process, single node):**

All threads share the same memory space. Fast (no data copying), but you must ensure no two threads write to the same location.

Use for:
- VFI inner loops where each grid point reads `V_old` and writes to a separate slot of `V_new`
- Simulating N independent firms that share parameter values but draw independently
- Computing moments analytically or from a simulated panel (parallel reduction)
- Any loop where iterations are independent but need to read a large shared array

Two dispatch mechanisms:
- `Threads.@threads for i in 1:N` -- static scheduling, splits iterations evenly across threads. Best when per-iteration cost is uniform (e.g., grid points in VFI).
- `Threads.@spawn` -- task-based, dynamic scheduling. Best when per-iteration cost varies (e.g., some firms converge faster than others). Wrap in `@sync` to wait for completion.

```julia
# Static: VFI over grid points (uniform cost per point)
Threads.@threads for i in 1:n_grid
    V_new[i] = bellman_operator(V_old, grid[i], params)
end

# Dynamic: simulation with variable cost per firm
@sync for i in 1:n_firms
    Threads.@spawn simulate_firm!(panel, i, params, rngs[Threads.threadid()])
end
```

Caution:
- Thread-local RNGs required. Never share a single RNG across threads.
- No two threads may write to the same array element. Pre-partition output arrays.
- `Threads.nthreads()` returns the thread count; set via `julia --threads=auto` or `JULIA_NUM_THREADS`.
- Garbage collector pauses affect all threads simultaneously. Minimise allocations in threaded loops.

**Distributed.jl (separate processes, can span multiple nodes):**

Each worker is an independent Julia process with its own memory. Data must be explicitly sent to workers. No shared mutable state, so no data races, but higher overhead per task.

Use for:
- Multiple optimizer restarts from different starting points (embarrassingly parallel)
- Bootstrap standard errors (each replication is independent)
- Parameter sweeps (evaluate objective at many parameter vectors)
- Any task where each unit of work is self-contained and needs no shared state
- Multi-node cluster jobs where Threads cannot reach across nodes

```julia
using Distributed
addprocs(4)  # or read from SLURM: addprocs(SlurmManager(parse(Int, ENV["SLURM_NTASKS"])))

@everywhere using MyModel  # load code on all workers

# Multiple optimizer restarts
results = pmap(1:n_starts) do i
    seed = base_seed + i
    run_estimation(params, seed=seed, grid_size=:full)
end
best = argmin(r -> r.objective, results)
```

Caution:
- `@everywhere` must precede any function or module used on workers
- Data sent to workers is serialised (slow for large arrays). Send parameters, not datasets. Workers should load data locally.
- `pmap` handles load balancing automatically; prefer it over `@distributed` for variable-cost tasks
- `@distributed (+) for i in 1:N` is useful for parallel reductions (summing across bootstrap replications)

**Decision rule:**

| Question | Answer | Use |
|----------|--------|-----|
| Do tasks need to read a large shared array? | Yes | Threads |
| Is each task self-contained (no shared mutable state)? | Yes | Distributed |
| Single node, many cores? | Yes | Threads (lower overhead) |
| Multiple nodes on a cluster? | Yes | Distributed (only option) |
| VFI inner loop? | -- | Always Threads |
| SMM optimizer restarts? | -- | Always Distributed |
| Bootstrap SEs? | -- | Distributed (or Threads if single-node) |
| Moment computation from simulated panel? | -- | Threads (shared panel array) |

**Combining both:** A common pattern in structural estimation runs Distributed across optimizer restarts, and inside each restart the VFI/simulation uses Threads. This works because each Distributed worker is a separate process with its own thread pool.

```julia
# Outer: Distributed across starting points
results = pmap(starting_points) do θ_init
    # Inner: Threads within VFI and simulation
    model = solve_model(θ_init)  # VFI uses Threads.@threads internally
    moments = simulate_and_compute(model)  # simulation uses Threads.@threads
    objective(moments, data_moments)
end
```

Set threads per worker via: `addprocs(n, exeflags="--threads=8")`.

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
Model/scripts/  run_estimation.jl | run_simulation.jl | run_counterfactual.jl | run_estimation_cluster.jl
Model/cluster/  submit_estimation.sh | submit_counterfactual.sh
```

## 10. Prohibited Patterns
- Global mutable state in hot paths
- `Any` typed containers or untyped struct fields
- Growing arrays in VFI loops (pre-allocate)
- Hardcoded absolute paths
- `eval`/`include` inside functions

## 11. Cluster Computing

Structural estimation often exceeds what a laptop can handle in reasonable time. These conventions ensure code runs correctly on both local machines and HPC clusters.

### Local-First Principle

All code must run locally on a coarse grid before being submitted to a cluster. The local run validates logic; the cluster run delivers precision.

```julia
# In run_estimation.jl (local entry point):
const GRID_SIZE = :coarse   # 50 grid points, 100 firms, 5 optimizer evals
include("../src/main_module.jl")
run_estimation(grid_size=GRID_SIZE)

# In run_estimation_cluster.jl (cluster entry point):
const GRID_SIZE = :full     # 500 grid points, 10_000 firms, full CMA-ES
include("../src/main_module.jl")
# ... plus checkpointing, walltime awareness, distributed setup
run_estimation(grid_size=GRID_SIZE)
```

Both entry points call the same `Model/src/` code. The difference is configuration, not logic.

### Entry Point Convention

- `Model/scripts/run_estimation.jl` -- local, coarse grid, for testing and debugging
- `Model/scripts/run_estimation_cluster.jl` -- cluster, full grid, with checkpointing and Distributed/SLURM setup
- Same pattern for `run_counterfactual.jl` / `run_counterfactual_cluster.jl` if counterfactuals are expensive
- SLURM job scripts go in `Model/cluster/`

### SLURM Job Scripts

Template for single-node threaded jobs:

```bash
#!/bin/bash
#SBATCH --job-name=estimation
#SBATCH --output=Output/slurm/%j.out
#SBATCH --error=Output/slurm/%j.err
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=32
#SBATCH --mem=64G
#SBATCH --time=24:00:00

module load julia/1.11.6  # adjust to cluster

cd $SLURM_SUBMIT_DIR
mkdir -p Output/slurm

julia --project=Model --threads=auto \
    Model/scripts/run_estimation_cluster.jl
```

Template for multi-node Distributed jobs:

```bash
#!/bin/bash
#SBATCH --job-name=estimation-distributed
#SBATCH --output=Output/slurm/%j.out
#SBATCH --error=Output/slurm/%j.err
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=16
#SBATCH --mem-per-cpu=4G
#SBATCH --time=48:00:00

module load julia/1.11.6

cd $SLURM_SUBMIT_DIR
mkdir -p Output/slurm

julia --project=Model --threads=16 \
    Model/scripts/run_estimation_cluster.jl --distributed
```

Inside the cluster entry point:

```julia
using Distributed, ClusterManagers

if "--distributed" in ARGS
    n_tasks = parse(Int, get(ENV, "SLURM_NTASKS", "1"))
    if n_tasks > 1
        addprocs(SlurmManager(n_tasks - 1), exeflags="--project=Model --threads=auto")
    end
end

@everywhere using MyModel
```

### Path Handling

Code must resolve paths on both local machines and cluster nodes. Cluster jobs typically write to a scratch filesystem.

```julia
const OUTPUT_DIR = if haskey(ENV, "SCRATCH")
    joinpath(ENV["SCRATCH"], "project_name", "output")
elseif haskey(ENV, "SLURM_JOB_ID")
    joinpath(pwd(), "Output", "cluster")
else
    joinpath(@__DIR__, "..", "..", "Output")
end
mkpath(OUTPUT_DIR)
```

### Checkpointing

Any estimation run exceeding 1 hour must checkpoint. SLURM jobs can be killed at walltime with no warning.

```julia
function save_checkpoint(path::String; iteration, params, obj_value, best_params, best_obj)
    tmp = path * ".tmp"
    JLD2.jldsave(tmp;
        iteration, params, obj_value, best_params, best_obj,
        timestamp = Dates.now()
    )
    mv(tmp, path, force=true)  # atomic replace prevents half-written files
end

function load_checkpoint(path::String)
    isfile(path) || return nothing
    JLD2.jldopen(path, "r") do f
        (iteration=f["iteration"], params=f["params"],
         best_params=f["best_params"], best_obj=f["best_obj"])
    end
end
```

Write to a temporary file then rename. This prevents a corrupted checkpoint if the job is killed mid-write.

Checkpoint frequency: every N optimizer evaluations or every T minutes, whichever comes first. A reasonable default is every 50 evaluations or every 15 minutes.

### Walltime Awareness

The cluster script must track elapsed time and save before SLURM kills the job.

```julia
const START_TIME = time()
const SLURM_TIMELIMIT = if haskey(ENV, "SLURM_JOB_ID")
    23.5 * 3600  # 30 min buffer on a 24h job; adjust per submission
else
    Inf  # no limit locally
end

function time_remaining()
    return SLURM_TIMELIMIT - (time() - START_TIME)
end

# Inside estimation loop:
if time_remaining() < 1800  # 30 min safety buffer
    @warn "Approaching walltime, checkpointing and exiting"
    save_checkpoint(checkpoint_path; iteration=iter, params=θ,
                    obj_value=obj, best_params=θ_best, best_obj=obj_best)
    break
end
```

### Thread-Local Random Number Generators

When using Threads for simulation, each thread needs its own RNG to avoid data races and ensure reproducibility.

```julia
using Random

function make_thread_rngs(base_seed::Int)
    [MersenneTwister(base_seed + t) for t in 1:Threads.nthreads()]
end

# Usage in threaded simulation:
rngs = make_thread_rngs(42)
Threads.@threads for i in 1:n_firms
    rng = rngs[Threads.threadid()]
    simulate_firm!(panel, i, params, rng)
end
```

For Distributed workers, each worker seeds independently:

```julia
@everywhere begin
    using Random
    Random.seed!(myid() * 1000 + base_seed)
end
```

### Cluster Prohibited Patterns

- Hardcoded number of workers (`addprocs(8)`). Read from `nprocs()`, `Threads.nthreads()`, or `ENV["SLURM_NTASKS"]`.
- Assuming that packages are installed. Always check and install via pck if necessary.
- Writing to `$HOME` on cluster nodes. Use `$SCRATCH` or `$TMPDIR`.
- Missing `@everywhere` before function definitions used on workers.
- No intermediate logging. Provide updates of the estimation process.
- No checkpointing in runs exceeding 1 hour.
- No walltime awareness in SLURM jobs.
- Sharing a single RNG across threads.
- Loading full dataset on every Distributed worker when only parameters are needed.
