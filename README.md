# GAMI: Geometry-Aware MILP Initialization for the Min-Max MDMTSP

Companion repository for the paper *"Geometry-Aware MILP Initialization for the Min-Max Multi-Depot Multiple Traveling Salesman Problem"* (under review).

GAMI builds high-quality initial solutions for the min-max multi-depot multiple traveling salesman problem (Min-Max MDMTSP) by combining Delaunay triangulation, size-capped Kruskal clustering, and MILP-based load-balanced assignment. The initialization is integrated, in a plug-and-play manner, into the mEAX memetic search framework of He & Hao (EJOR, 2023).

> **Source code availability**: the full C++ source code (GAMI initialization and all ablation variants) will be released here upon paper acceptance. Pre-compiled Linux x86-64 executables are provided below so that all reported results can be re-run and verified.

## Repository layout

```
bin/        pre-compiled executables (Linux x86-64)
data/       benchmark instances (Set M: 43, Set P: 23)
results/    all experiment result data (CSV)
```

## Executables (`bin/`)

### `ma_original` — MA baseline (He & Hao 2023, official binary)

```
./ma_original <instance> <time_limit_s> <max_iterations> <seed>
# example: ./ma_original data/set_m/MS1d.txt 60 -1 1
```
`max_iterations = -1` means time-only termination. `time_limit_s = 0` outputs the initial solution.

### `gami_v14_hybrid` — GAMI initialization + mEAX search (this paper)

```
./gami_v14_hybrid <instance> <time_limit_s> <seed> <flag>
# example: ./gami_v14_hybrid data/set_m/MS1d.txt 60 1 0
```
`flag` is reserved, use `0`. `time_limit_s = 0` outputs the initial solution.
The MILP assignment uses CPLEX (statically linked into the binary; no CPLEX installation required at runtime). Per-run CPLEX settings: single thread, 10 s time limit, 1% optimality gap.

Both programs print the final objective as `The best solution is <value>`.

## Benchmark instances (`data/`)

- **Set M** (43 instances, `data/set_m/`): Wang, Golden & Wasil (2015), n = 10–500 cities, 1–20 depots. Main benchmark of the paper.
- **Set P** (15 instances, `data/set_p/`): converted from the classic Cordeau et al. (1997) MDVRP benchmark (p01–p23). Conversion: customer and depot coordinates and the per-depot vehicle number `m` are taken verbatim from the original files; the capacity (Q) and route-duration (D) constraints are removed to obtain Min-Max MDMTSP instances. Since p12–p14, p15–p17, p18–p20 and p21–p23 differ **only** in the removed D/Q constraints, each group collapses to a single instance after conversion; the de-duplicated set therefore contains 15 distinct instances (p01–p12, p15, p18, p21), n = 50–360, 2–9 depots, 8–45 salesmen. Supplementary benchmark.

Format: header line `name n n_depots reserved`, then one line per depot (`id x y m_k`), then one line per city (`id x y flag`).

## Experiment data (`results/`)

All comparisons use the protocol: per instance × 10 random seeds (1–10), independent single-thread runs at time limits {0, 15, 30, 60} s. Improvement% = (MA − GAMI)/MA × 100, positive = GAMI better.

| File | Content | Supports |
|---|---|---|
| `setm_gami_v14_detail.csv` | GAMI+mEAX on Set M: per instance × time_limit, seeds s1–s10, mean/std/best | main comparison table, per-instance table, convergence figure, CV analysis |
| `setm_ma_original_detail.csv` | MA baseline on Set M, same format | same |
| `setm_factorial_3x2.csv` | 3 (adjacency: Delaunay/kNN/random) × 2 (assignment: MILP/random) factorial, independent runs at 4 time limits; row = one run, last column = objective (`NA` marks 9 missing runs; affected cells average 9 seeds) | factorial ablation table |
| `setm_knn_kscan.csv` | kNN adjacency with k ∈ {4,6,8,10,12}, t=0 | kNN k-scan |
| `setm_weight_sensitivity.csv` | depot-distance weight w ∈ {0,0.1,0.25,0.5,0.75,1.0}, one 60 s run per row with checkpoint objectives | weight sensitivity table |
| `setm_restart_ablation.csv` | restart configs {none, greedy η=2000, GAMI η∈{500,2000,5000}}; `gami_restart_eta2000_default` rows are the same runs as `w0.25` in the weight file (shared default config) | restart ablation table |
| `setm_init_time.csv` | initialization wall-time per instance (MA vs GAMI) | time-structure analysis |
| `setm_instance_params.csv` | per-instance n / n_depots / m | feature correlation analysis |
| `adjacency_build_bench.csv` | adjacency-graph construction time vs n (naive/fast Delaunay, kNN) | scalability analysis |
| `setp_*` | Set P results (MA vs GAMI, same protocol) | Set P supplementary table |

**Note on two t=0 measurements.** In `setm_*_detail.csv`, `time_limit=0` rows are *independent runs* that output the initialization result. In `setm_weight_sensitivity.csv` / `setm_restart_ablation.csv`, `obj_t0_checkpoint` is the checkpoint recorded *inside a 60 s run* at the moment population construction completes (all individuals already VND-refined). The two t=0 definitions differ by design and must not be compared across files.

## References

- He P., Hao J.-K. Memetic search for the min-max multiple traveling salesman problem with single and multiple depots. *European Journal of Operational Research*, 2023, 307(3): 1055-1070.
- Wang X., Golden B.L., Wasil E.A. The min-max multi-depot vehicle routing problem: heuristics and computational results. *Journal of the Operational Research Society*, 2015, 66(9): 1430-1441.
- Cordeau J.-F., Gendreau M., Laporte G. A tabu search heuristic for periodic and multi-depot vehicle routing problems. *Networks*, 1997, 30(2): 105-119.
