# ELE8101 CW2 — Vehicle State Estimation on a Racing Track

**Module:** ELE8101 Estimation Theory | Queen's University Belfast | Spring 2026

**Group members:**
- ChenhuiCui (Member A) — Modelling
- Weiting Wu (Member B) — EKF Estimator

---

## Repository Structure

| File | Member | Description |
|---|---|---|
| `modelling.ipynb` | A | Track geometry (A1), vehicle motion model (A2), beacon placement (A3), noise validation (A4), sensor bias handling (A5) |
| `ekf.ipynb` | B | EKF implementation and simulation (B1–B4) |
| `figures/` | Both | Generated plots referenced in the report |

---

## How to Run

### Dependencies
pip install numpy matplotlib casadi

### Member A — Modelling

Open and run all cells in `modelling.ipynb` (top-to-bottom).
All figures are saved to `figures/` and referenced in the report.

### Member B — EKF

Open and run all cells in `ekf.ipynb`.

---

## Key Results (Modelling)

### Track and Model

| Parameter | Value |
|---|---|
| Track perimeter *L* | 426.7104 m |
| Centreline radii | *R*ₐ = 48 m, *R*ᵦ = 22 m |
| Straight inclination α | 15.0701° |
| Sampling time *h* | 0.01 s (100 Hz) |
| State vector | *z* = [*s*, *ℓ*, *v*ₛ, *v*ₗ]ᵀ |
| Process noise *Q* | diag(0.5, 0.002) |
| Measurement noise *R* | 2.25 m² (σᵧ = 1.5 m) |

### Bound-respecting dynamics

Physical bounds are enforced **by model structure** (not by post-hoc clipping):

- **ℓ** bounded by nonlinear stiffness *k*(*ℓ*) that diverges as \|*ℓ*\| → 2 m
- **v**ₗ soft-saturated via tanh at ±0.55 m/s
- **v**ₛ soft-saturated via tanh in (0, 25) m/s

Monte Carlo verification (100 trials × 3000 steps, no clipping):

| Constraint | Result | Bound | Status |
|---|---|---|---|
| max \|ℓ\| | 0.109 m | 2.0 m | ✓ |
| max \|*v*ₗ\| | 0.335 m/s | 0.55 m/s | ✓ |
| max *v*ₛ | 20.08 m/s | 25 m/s | ✓ |
| min *v*ₛ | 5.05 m/s | > 0 | ✓ |

### Beacon placement (GDOP-optimised)

| Metric | Value |
|---|---|
| Number of beacons | 5 |
| Mean GDOP | 0.9193 |
| Max GDOP | 0.9770 |
| % of track with GDOP < 1.0 | 100% |
| Installation boundary | 30 m (Pareto-optimal) |

**Beacon coordinates (m):**
B1: (-21.1,  73.3)
B2: (121.1,  46.7)
B3: ( 26.3, -60.0)
B4: (-52.6,  20.0)
B5: ( 89.5, -46.7)

### Sensitivity

Monte Carlo installation-error analysis (200 realisations, σ = 5 m per coordinate):

| Metric | Value |
|---|---|
| Median GDOP | 0.9205 (+0.1%) |
| 95th percentile | 0.9308 (+1.2%) |
| Worst case | 0.9368 (+1.9%) |
| Pr(mean GDOP < 1) | 100% |

---

## Branch Structure

- `main` — integrated version for submission
- `ChenhuiCui` — Member A (modelling) development branch
- `member-B-Weiting-Wu` — Member B (EKF) development branch

---

## Notes

- All saturations are C∞ (smooth) for compatibility with EKF Jacobian linearisation.
- Member A exports `f_dynamics(z, w)` and `A_jacobian(z_hat)` for use by Member B's EKF.
- Historical commits prior to April 1, 2026 may display under a legacy local username `cch` due to an early git config issue; all subsequent activity is correctly attributed to `cuichenhui9-eng`. See report appendix.