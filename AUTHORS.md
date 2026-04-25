# Authors and File Attribution

This document provides an authoritative record of file ownership 
in this repository.

## Member A — Chenhui Cui
GitHub: [@cuichenhui9-eng](https://github.com/cuichenhui9-eng)

- `Modelling.ipynb` — complete modelling pipeline
  (A1 track geometry, A2 bound-respecting vehicle motion, A3 beacon
  placement via GDOP, A4 noise-parameter validation, A5 sensor-bias
  state augmentation)
- `report/main.tex` — Section 1 (Modelling), AI-usage statement, all
  appendices
- `Figures_Modelling/*.png` — all figures referenced in Section 1
- `.mailmap` — author identity consolidation
- `Estimator.ipynb` — final consolidated estimator (CasADi-Jacobian
  prediction step, unified bound-respecting truth model, circular
  arc-length error metric, per-step EKF runtime benchmark, nonlinear
  MHE constraint, default-rng-seeded reproducibility,
  `results_summary.json` export)
- `Figures_Estimator/*.png` — final figures regenerated from the
  consolidated `Estimator.ipynb`

## Member B — Weiting Wu
GitHub: [@wu1826036713-ship-it](https://github.com/wu1826036713-ship-it)

- `Estimator.ipynb` — original B1–B4 cell structure and first-pass
  implementation (single-run EKF, Monte-Carlo loop, MHE comparison,
  bias-augmented EKF). Subsequent revisions to the prediction step,
  truth model, MHE constraint, anchor-count ablation, and reproducibility
  layer are by Member~A and recorded in the `git log`.
- `report/main.tex` — Section 2 (EKF) and Section 3 (MHE Comparison)
  text content from his standalone draft, integrated and revised
  by Member~A.

## Joint contributions
- Abstract and Conclusion in `report/main.tex`
- Cross-validation of Lyapunov prediction vs Monte-Carlo results
- `README.md`

## Contribution statistics
Running `git shortlog -sn`:
- Member A: 43 commits
- Member B: 19 commits

See Appendix A.3 of the final report for details.
