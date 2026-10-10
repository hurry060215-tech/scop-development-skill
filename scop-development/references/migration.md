# Algorithm migration and performance

Apply only to an authorized migration/performance task. Align computation first, validate numerical behavior second, and optimize last.

## Admission and implementation

- Migrate core algorithms only with an official reproducible reference and a demonstrated performance bottleneck or Python deployment obstacle. Do not directly translate complex external deep-learning models into C++.
- SCOP migrations retain the original R/Python backend and add `backend = "cpp"`; other packages retain their own interface contract. This backend value does not justify implementation suffixes in public function names.
- Preserve batching, waypoint selection, and other reference mechanisms. Do not silently introduce sampling or approximations.
- Compare stages in order: inputs/name mapping, preprocessing, numerical fitting, derived metrics, object write-back. Check losses, regularization, statistical denominators, pseudocounts, log bases, bandwidths, self-loops, and tie-breaking.
- Agreement between two internal implementations does not replace parity against the official upstream implementation. Record reference versions/commits and run a real end-to-end case.

## Numerical acceptance

| Output | Acceptance |
| --- | --- |
| Deterministic numbers | Declare absolute/relative tolerances appropriate to the algorithm and scale before interpreting results; compare elementwise errors and exceptional values |
| Labels, indices, schema | Exact agreement with the contract; only explicitly permitted permutations/order alignment |
| Graphs and rankings | Match nodes, edge weights/direction, self-loops, and tie-breaking; report recall/retention separately for authorized approximate retrieval |
| Stochastic algorithms/embeddings | Multiple fixed seeds and domain metrics such as ARI, neighborhood preservation, or Procrustes RMSE; report variation across seeds |

When a reference does not specify tolerances, do not invent an approved threshold. A conventional candidate is `abs(actual - reference) <= atol + rtol * abs(reference)`, with task-specific justified tolerances; handle NA/NaN/Inf and labels explicitly. A blanket correlation threshold such as 0.95 is not a substitute for numerical error analysis.

## Performance and evidence

Fix hardware, input size, seeds, and thread counts. Include a single-thread baseline and account for BLAS/OpenMP parallelism. Retain wall time, memory, and stage/bridge costs when relevant; do not attribute cross-language conversion overhead to the numerical kernel.

Keep evidence isolated; for SCOP use `tmp/<method>/` only if already excluded from distribution/version control, otherwise an external isolated directory. Save `meta.csv`, `metrics.csv`, `bench.csv`/`stage_profile.csv`, and `bridge_profile.csv` as needed, never as committed package test assets. If the change only removes a Python deployment obstacle, claim no unmeasured speedup.
