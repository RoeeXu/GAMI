# GAMI: Geometry-Aware MILP Initialization

Geometry-Aware MILP Initialization for the Min-Max Multi-Depot Multiple Traveling Salesman Problem.

## Benchmark Instances

This repository contains the 66 benchmark instances used in our experiments.

### Set M (43 instances)

From Wang et al. (2015). Located in `data/set_m/`.

**Format:** Each file starts with a header line:
```
instance_name  n_cities  n_salesmen  best_known_objective
```
Followed by depot coordinates, then city coordinates:
```
id  x  y  flag
```

| Scale | Instances | Cities | Depots |
|-------|-----------|--------|--------|
| Small | M1–M5 | 10–30 | 1–5 |
| Medium | M6–M24 | 50–200 | 2–10 |
| Large | M25–M43 | 300–500 | 5–20 |

### Set P (23 instances)

From Cordeau et al. (1997). Located in `data/set_p/`.

**Format:** Same as Set M. Originally designed for capacitated MDVRP; capacity constraints are removed for Min-Max MDMTSP testing.

| Instances | Cities | Depots |
|-----------|--------|--------|
| p01–p23 | 50–360 | 2–9 |

## References

- Wang, X., Golden, B.L., Wasil, E.A. (2015). The min-max multi-depot vehicle routing problem: Heuristics and computational results. *Journal of the Operational Research Society*, 66(9), 1430–1441.
- Cordeau, J.F., Gendreau, M., Laporte, G. (1997). A tabu search heuristic for periodic and multi-depot vehicle routing problems. *Networks*, 30(2), 105–119.

## Citation

If you use these instances in your research, please cite the original sources above.
