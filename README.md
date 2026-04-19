# ELE8101 CW2 — Vehicle State Estimation on a Racing Track

**Module:** ELE8101 Estimation Theory | Queen's University Belfast | Spring 2026

**Group members:**
- ChenhuiCui (Member A) — Modelling
- Weiting Wu (Member B) — EKF Estimator

---

## Repository Structure

| File | Member | Description |
|------|--------|-------------|
| `modelling.ipynb` | A | Track geometry, motion model, beacon placement, noise validation, bias handling (A1–A5) |
| `ekf.ipynb` | B | EKF implementation and simulation (B1–B4) |

---

## How to Run

### Dependencies
```bash
pip install numpy matplotlib casadi
```

### Member A — Modelling
Open and run all cells in `modelling.ipynb`

### Member B — EKF
Open and run all cells in `ekf.ipynb`

---

## Key Results (Modelling)

| Parameter | Value |
|-----------|-------|
| Track perimeter L | 426.710 m |
| Beacon count | 5 |
| Mean GDOP | 0.9193 |
| Max GDOP | 0.9770 |
| GDOP < 1.0 coverage | 100% |
| Sampling time h | 0.01 s (100 Hz) |
| Process noise Q | diag(0.5, 0.003) |

---
  
## Branch Structure

- `main` — final merged version
- `ChenhuiCui` — Member A development branch  
- `member-B-Weiting-Wu` — Member B development branch 
