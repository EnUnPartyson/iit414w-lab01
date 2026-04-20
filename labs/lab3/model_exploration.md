# Model Exploration — Lab 3 Initial Artifact
**IIT414W · Artificial Intelligence Workshop · 2026-1T**  
*In-class checkpoint artifact (W5 Monday, March 30, 2026)*

---

## 0. Framing Decision

- **Business question:** How many constructor-championship points will each driver score in the next race, given their current grid position and recent form?
- **Target:** `points` scored per race entry (0–25, continuous regression target)
- **Metric:** MAE (Mean Absolute Error). Appropriate because the unit is "points" — directly interpretable by the team. MAE penalises all errors equally, unlike RMSE which over-weights podium mispredictions.
- **Why this framing:** A team principal needs a *continuous* estimate to simulate championship points accumulation over a season. A binary "will score points?" model loses the P1 (25 pts) vs P10 (1 pt) distinction that determines strategy.
- **Rejected alternative:** Binary classification (top-10 yes/no) with macro F1. Rejected because a 1-point error in the points zone and a 25-point error in the podium zone are not equivalent strategically, are they are both label = 1 in a binary framing.

---

## 1. Models Trained

| Model | Key Hyperparameters | Features Used |
|---|---|---|
| B1 — Predict mean | strategy='mean' | None (statistical baseline) |
| B2 — Predict median | strategy='median' | None (statistical baseline) |
| B3 — Grid heuristic | lookup table from train | `grid` only |
| Ridge | alpha=1.0, random_state=414 | grid, position_lag_1, rolling_avg_pos_3, constructor_avg_grid_5, rolling_avg_pts_3, circuit_id (OHE), constructor_id (OHE) |
| Random Forest | n_estimators=100, max_depth=10, min_samples_leaf=5, random_state=414 | Same as Ridge (no StandardScaler) |
| Gradient Boosting | n_estimators=100, max_depth=4, learning_rate=0.1, random_state=414 | Same as RF |

---

## 2. Comparison Table (same metric, same validation)

| Model | Features | Validation | Train MAE | Test MAE | WHY this result |
|---|---|---|---|---|---|
| B1 — Predict mean | — | 2023–2024 | — | 5.923 | Predicts ~5.1 pts (training mean) for every entry; ~50% of drivers score 0 pts, so massively over-predicts most rows. |
| B2 — Predict median | — | 2023–2024 | — | 5.129 | Training median = 0; correct for most entries but wildly wrong for top finishers. |
| B3 — Grid heuristic | grid | 2023–2024 | 3.341 | 3.246 | Lookup table directly encoding grid→points curve. Hard to beat; the real minimum bar for ML. |
| Ridge (alpha=1.0) | all 5 numeric + OHE cats | 2023–2024 | 3.350 | 3.299 | Linear model; cannot model the nonlinear grid→points step function. **Does NOT beat the heuristic.** |
| Random Forest | all 5 numeric + OHE cats | 2023–2024 | 2.482 | 2.838 | Nonlinear thresholds capture P1–P3 disproportionate points. Mild overfitting (gap = +0.356). Beats heuristic by −12.6%. |
| Gradient Boosting | all 5 numeric + OHE cats | 2023–2024 | 2.378 | 2.827 | Sequential residual correction; best test MAE. Slightly more overfitting than RF (gap = +0.449). |

---

## 3. Best Model Justification

Gradient Boosting (test MAE = 2.827) edges Random Forest (2.838) as the best model. Both ensemble methods beat the grid heuristic by approximately 13% because they utilise features beyond just grid position: `rolling_avg_pts_3` (21% of RF feature importance) and `constructor_avg_grid_5` (29.6%) capture driver momentum and team pace that a grid-only lookup table cannot see.

The train-test gap for Gradient Boosting (+0.449, or ~16% of test MAE) indicates mild overfitting. This is expected: boosting sequentially learns from residuals, making it more sensitive to training-data noise than a regularised model like Ridge on small datasets (~2,000 rows). Random Forest's slightly smaller gap (+0.356) suggests it generalises marginally better, but both are within an acceptable range for this dataset size.

The critical finding is that Ridge **does not beat** the domain heuristic. This reveals a structural limitation: all the extra features Ridge has access to do not compensate for its inability to model nonlinear grid→points thresholds. This is an honest result, not a failure to engineer correctly.

---

## 4. One Honest Limitation

The grid heuristic (MAE = 3.246) is remarkably competitive against a full Ridge model (MAE = 3.299) trained on 5 features. The reason: grid position alone explains ~37% of variance in points (confirmed by RF feature importance). Both ensemble models improve on the heuristic, but only by 0.42 pts absolute — approximately one-seventh of a P10 finish. For a team principal asking whether this model is "worth deploying," the honest answer is: yes for aggregate season planning, no for race-by-race strategic decisions where a 2.8-point average error is large relative to individual race stakes (1–25 pt range). Additionally, all models miss race-day dynamics (safety cars, weather, mechanical failures) that can shift a driver 5–10 positions and are not in the historical dataset.
