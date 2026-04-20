# Framing Decision — Lab 3 Model Comparison
**IIT414W · Artificial Intelligence Workshop · 2026-1T**

---

## Business Question

How many constructor-championship points can a team expect from each driver in an upcoming race, given their starting grid position and recent form?

A team principal uses this estimate to compare driver contributions across a season, allocate strategy resources race by race, and decide when the current car package is delivering on its expected points ceiling — or falling short.

---

## Target Variable

**`points`** — the actual points scored by a driver in a single race (continuous, range 0–25, with bonus points for fastest lap in recent seasons).

Defined as: points awarded by the FIA scoring system (25-18-15-12-10-8-6-4-2-1-0) based on finishing position within the top 10.

---

## Metric

**Primary metric: MAE (Mean Absolute Error)**

MAE was chosen over RMSE because it penalises all errors equally regardless of magnitude: being wrong by 3 points on a midfield driver is treated the same as being wrong by 3 points on a race winner. RMSE would punish large errors (podium mispredictions) disproportionately and make the metric harder to interpret in pts terms. MAE's unit is literally "points" — a MAE of 2.8 means the model is off by 2.8 points per entry on average, which is directly meaningful to a strategist.

The metric is consistent across ALL rows in the comparison table. Baselines and ML models are evaluated on the same test set (seasons 2023–2024) using MAE.

---

## Rejected Alternative: Binary Classification (Top-10 Finish)

The most natural alternative framing is binary classification: "Will this driver score points?" (top-10 = yes/no), evaluated with macro F1-score.

**Reason for rejection:** Binary classification loses all distinction within the points zone. P1 (25 pts) and P10 (1 pt) both map to label = 1, but their contribution to the constructor championship differs by 25×. A team managing a championship battle cannot use a binary model to simulate multi-race points accumulation or to decide whether a risky pit strategy targeting P2 instead of P5 is worth the gamble.

**Why it has surface appeal:** The ~50% zero-point rate makes this a balanced binary problem, and F1-score is intuitive. But the business question — *how many points* — requires a continuous prediction. Accuracy of "will they score?" is a necessary but not sufficient answer.

**Second rejected alternative — multiclass (no_points / scoring / podium):** Provides more granularity than binary but still collapses P1 (25 pts) and P3 (15 pts) into the same "podium" bucket. The boundaries are arbitrary, and the podium class has ~15% prevalence, creating a class-imbalance problem that would require calibration work beyond the scope of this lab.
