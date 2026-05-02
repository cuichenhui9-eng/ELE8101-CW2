# Nonlinear State Estimation: EKF and MHE with CasADi

A Python implementation and side-by-side comparison of two nonlinear
state estimators on a 2D vehicle-localisation benchmark from
range-only measurements:

- an **extended Kalman filter (EKF)** with per-step Jacobians obtained
  from **CasADi automatic differentiation**, ensuring the predictor
  and the simulator share identical dynamics;
- a **moving-horizon estimator (MHE)** with the same nonlinear
  dynamics constraint and an analytical Jacobian.

Both estimators are validated by **100-trial Monte-Carlo simulation**,
a Normalized Innovation Squared consistency check, parameter
sensitivity scans across four orders of magnitude, and a
master-seed reproducibility chain that guarantees byte-identical
results on re-execution. A 9-state augmented EKF additionally
demonstrates online sensor-bias estimation.

The work was originally developed for ELE8101 Estimation Theory at
Queen's University Belfast (Spring 2026); the report and meeting
minutes appendix are kept for context. Authors: Chenhui Cui and
Weiting Wu.

---

## Repository structure

| Path | Description |
|---|---|
| `Modelling.ipynb` | Track geometry (A1), bound-respecting nonlinear vehicle motion model (A2), beacon placement optimisation (A3), noise-parameter validation (A4), sensor-bias state augmentation (A5). |
| `Estimator.ipynb` | EKF B1 single-run · B2 Monte-Carlo + NIS · B3 MHE comparison · B4 9-state augmented EKF · B5 beacon-count ablation · B6 near-boundary stress test, with sensitivity scans for `P0` and `q_β`, a closed-loop covariance-trace plot, and a linear-vs-nonlinear truth-model comparison. |
| `Figures_Modelling/` | Figures produced by `Modelling.ipynb` (referenced in Section 1 of the report). |
| `Figures_Estimator/` | Figures produced by `Estimator.ipynb` (referenced in Sections 3–5 of the report). |
| `results_summary.json` | Auto-exported summary of every numerical claim in the report; regenerated on each notebook run, used to verify that the report's quoted numbers and the live notebook outputs do not drift. |
| `report/main.tex` | Integrated LaTeX report (modelling + estimator + appendix). Compile with XeLaTeX. |
| `AUTHORS.md` | File-level contribution attribution. |
| `.mailmap` | Author-identity consolidation (corrects an early local-username mis-configuration). |

---

## How to run

### Dependencies

```bash
pip install numpy matplotlib casadi scipy jupyter nbconvert
```

The notebooks were developed and tested with Python 3.13, NumPy ≥ 2.3,
SciPy ≥ 1.16, CasADi 3.7.

### Modelling

```bash
jupyter nbconvert --to notebook --execute --inplace Modelling.ipynb
```

Figures land in `Figures_Modelling/`. The notebook is fully
deterministic when its internal `MASTER_SEED` is left at its default.

### Estimator

```bash
jupyter nbconvert --to notebook --execute --inplace Estimator.ipynb \
        --ExecutePreprocessor.timeout=2400
```

End-to-end runtime is roughly fifteen minutes; the dominant cost is
the MHE comparison cell (B3), which solves a constrained nonlinear
program at every horizon step. Figures land in `Figures_Estimator/`
and a JSON summary lands in `results_summary.json`.

Reproducibility is guaranteed by a single top-level `MASTER_SEED`
plus per-cell `np.random.default_rng(SEED_B<n> + trial)` instances:
re-running the notebook from a clean state produces byte-identical
figures and a byte-identical `results_summary.json`.

### Compiling the report

```bash
cd report
xelatex -interaction=nonstopmode main.tex
xelatex -interaction=nonstopmode main.tex   # second pass for refs
```

XeLaTeX is required because the preamble uses `fontspec` /
`\setmainfont{Times New Roman}`. The graphics path is configured for
both `report/` and the repository root, so figures are picked up
without copying.

---

## Key numerical results

### Modelling

| Parameter | Value |
|---|---|
| Track perimeter L | 426.7104 m |
| Centreline radii | R\_A = 48 m, R\_B = 22 m |
| Straight inclination α | 15.0701° |
| Sampling period h | 0.01 s (100 Hz) |
| State vector | z = [s, ℓ, v\_s, v\_ℓ]ᵀ |
| Process noise Q | diag(0.5, 0.002) |
| Measurement noise R | 2.25 m² (σ\_y = 1.5 m) |
| Beacon count | 5 (mean GDOP 0.9193; 100% of track at GDOP < 1) |
| Beacon coordinates (m) | (-21.1, 73.3), (121.1, 46.7), (26.3, -60.0), (-52.6, 20.0), (89.5, -46.7) |

Bound-respecting dynamics: ℓ confined by a nonlinear stiffness k(ℓ)
that diverges as |ℓ| → 2 m; v\_s and v\_ℓ confined by smooth
tanh saturations. Monte-Carlo verification over 100 × 3000 steps
shows max |ℓ| = 0.109 m, max |v\_ℓ| = 0.335 m/s — well inside
the physical 2 m / 0.55 m/s bounds with no post-hoc clipping.

### Estimator (Monte-Carlo, 100 trials)

| Metric | Value |
|---|---|
| Mean arc-length error | 0.226 m |
| Mean lateral error | 0.022 m |
| EKF / single-shot GDOP benchmark | ≈ 6.1× smaller (0.226 m vs 1.379 m) |
| NIS empirical mean | 4.994 (theoretical 5.000; 95.0% in the 95% band) |
| Closed-loop covariance-trace decay | ≈ 5× across the 5 s warm-up window |
| Per-step EKF runtime | 0.43 ms (4.3% of the 10 ms budget) |
| MHE solve time vs budget | 1275 ms (128× over budget; 0/490 SLSQP failures) |
| 9-state augmented EKF max bias-recovery error | 0.0354 m for biases up to ±0.8 m |
| q\_β sensitivity (1 e-8 to 1 e-5) | bias-recovery error stays below 0.12 m |
| P\_0 scaling (×0.1 to ×5) | steady-state arc error invariant to 1 e-7 m |

All numbers are exported on every notebook run to
`results_summary.json` and cited verbatim in `report/main.tex`.

---

## Branch layout

| Branch | Purpose |
|---|---|
| `main` | Integrated submission state. |
| `ChenhuiCui` | Modelling-side development; later carried the final estimator-notebook consolidation and report integration. |
| `WeitingWu` | Estimator-side first-pass development. Frozen at the last estimator-side commit so that the boundary between the two members' work is unambiguous in the git log. |

The `WeitingWu` branch is intentionally not advanced beyond its last
commit; the final submission state lives entirely on `main` /
`ChenhuiCui` after the 25 April integration round.

---

## Author identity

Both authors are credited via `.mailmap`:

```
Chenhui Cui <cuichenhui9@gmail.com>   (GitHub: cuichenhui9-eng)
Weiting Wu  <wu18875929815@outlook.com>   (GitHub: wu1826036713-ship-it)
```

A small number of early commits were authored under the legacy local
username `cch` due to a `git config` mis-configuration on one
machine; `.mailmap` consolidates these back to Chenhui Cui's primary
identity. See `AUTHORS.md` and Appendix A.2 of the report for
details.

---

## Reproducibility checklist

- A single `MASTER_SEED` controls all stochastic experiments.
- Both notebooks call `np.random.default_rng` with explicit per-cell
  seeds (no reliance on the global RNG).
- Every numerical claim in the report is exported to
  `results_summary.json` and re-checked on each run.
- All figures are regenerated from the notebooks; nothing is checked
  in by hand.
- Compilation requires only XeLaTeX with system Times New Roman; no
  CTAN package outside BasicTeX is needed.
