# Comparison Table — Lab 3 Model Comparison
**IIT414W · Artificial Intelligence Workshop · 2026-1T**

---

## Setup

| Field | Value |
|---|---|
| **Target** | `points` scored per race entry (0–25, continuous) |
| **Primary metric** | MAE — Mean Absolute Error (unit: points) |
| **Train set** | Seasons 2018–2022 · 2,022 rows |
| **Test set** | Seasons 2023–2024 · 908 rows |
| **Validation type** | Temporal split (no random shuffling) |
| **RANDOM_SEED** | 414 (all `random_state=` arguments) |

> **Rule:** Every row uses the same metric (MAE) and the same test set (2023–2024). Comparing models with different splits would be invalid regardless of how many were trained.

---

## Results

| Model | Type | Train MAE | Test MAE | Gap (Test − Train) | WHY this result |
|---|---|---|---|---|---|
| B1 — Predict mean | Statistical baseline | — | **5.923** | — | Predicts ~5.1 pts for every entry; ~50% of drivers score 0 pts, so the mean dramatically over-predicts most rows. Highest error in the table. |
| B2 — Predict median (0) | Statistical baseline | — | **5.129** | — | Training median = 0 pts. Correctly predicts zero-point entries (majority class), but wildly wrong for any top-10 finish. Better than mean but still useless. |
| B3 — Grid heuristic | Domain-knowledge baseline | 3.341 | **3.246** | −0.096 | Lookup table: maps each grid position to its average historical points (built on training data only). Directly encodes the grid→points nonlinearity. Tiny negative gap means the 2023–2024 test set is marginally easier to predict than the training data. The real bar for any ML model. |
| Ridge (alpha=1.0) | Linear regression + L2 | 3.350 | **3.299** | −0.051 | Uses all 5 numeric features + OHE categoricals, but the linear assumption limits performance. The grid→points curve is strongly nonlinear (P1 earns 25 pts, P2 earns 18, but P9 earns 2 and P10 earns 1). Ridge cannot express thresholds — it applies a constant slope across the entire grid range. **Result: Ridge does NOT beat the domain heuristic** (3.299 vs 3.246). The extra features do not compensate for the mismatched model family. Negative gap indicates very slight underfitting, not overfitting. |
| Random Forest (n=100) | Nonlinear ensemble | 2.482 | **2.838** | +0.356 | 100 decision trees learn nonlinear thresholds (e.g., grid ≤ 3 → high points, grid > 10 → near-zero). Beats the heuristic by 0.408 pts (−12.6%). Feature importances confirm grid (37.2%), constructor_avg_grid_5 (29.6%), and rolling_avg_pts_3 (21.0%) dominate. The train-test gap of +0.356 (13% of test MAE) signals mild overfitting on 2,022 rows; constraints (max_depth=10, min_samples_leaf=5) limit but do not eliminate it. |
| Gradient Boosting (n=100) | Nonlinear ensemble | 2.378 | **2.827** | +0.449 | Sequential residual correction — each of 100 shallow trees (depth=4) learns what the previous got wrong. Edges Random Forest by 0.011 pts (−0.4%) on the test set. The larger gap (+0.449 vs RF's +0.356) indicates slightly more overfitting, which is expected: boosting is more expressive than bagging and more sensitive to training-set noise on small datasets. **Best test MAE overall.** |

---

## Key Observations

1. **Ridge fails to beat the domain heuristic.** This is an honest result: a linear model with multiple features (MAE = 3.299) performs worse than a simple lookup table (MAE = 3.246). The grid→points relationship is nonlinear, and Ridge cannot model that without additional feature transformations.

2. **Nonlinear ensembles clearly beat the heuristic.** Both RF and GBR reduce test MAE to ~2.83 (a ~13% improvement over the heuristic). This confirms that driver momentum (`rolling_avg_pts_3`) and team form (`constructor_avg_grid_5`) carry information beyond what grid position alone captures.

3. **GBR is the best model, but only marginally better than RF.** The 0.011 pt difference in test MAE is barely worth the higher train-test gap. In production, RF would be the more conservative choice (smaller gap, similar test MAE).

4. **The negative gap for Grid heuristic and Ridge** is not "negative overfitting" — it reflects that the test seasons have a distribution slightly closer to the training mean than the training seasons do. The training data includes the 2022 regulation reset, which was a period of unusual performance distribution.

---

## Figure References

- `comparison_bar.png` — Bar chart of test MAE across all 6 models
- `overfitting_gap.png` — Train vs. test MAE grouped bar chart (overfitting diagnostic)
- `rf_importance.png` — Random Forest top-12 feature importances
- `predicted_vs_actual.png` — Predicted vs. actual points scatter for best model (GBR)
