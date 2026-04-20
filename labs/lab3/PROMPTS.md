# PROMPTS.md — AI Usage Documentation
**IIT414W · Artificial Intelligence Workshop · 2026-1T — Lab 3**

---

> **Authenticity standard:** This log documents AI use with traceability to notebook cells, operational detail (specific metric values, errors encountered), and critical distance. Format alone does not make this log valid — the entries below reflect what actually happened during development.

---

## Entry 1 — Initial notebook structure and data loading

| Field | Detail |
|---|---|
| **Context** | Needed to create the main `lab3_model_comparison.ipynb` notebook, starting from the W05 reference notebook structure. Was adapting from W05_Mon_supervised_models_v2.ipynb which already had Jolpica API data loading. |
| **Prompt** | Asked AI to scaffold the full lab notebook: data loading (with cache), feature engineering (same 5 features from W05), temporal split (2018–2022 train / 2023–2024 test), 6 models including GBR, comparison table, and 4 diagnostic plots. |
| **AI Output** | Generated full notebook JSON with cells for: seed/imports, data loading with Jolpica pagination, feature engineering (position_lag_1, rolling_avg_pos_3, constructor_avg_grid_5, rolling_avg_pts_3), temporal split, preprocessors, 3 baselines, Ridge, RF, GBR, comparison DataFrame, 4 matplotlib figures. |
| **Validation** | Ran all cells sequentially. Data loading cell took 44s and downloaded 2,979 rows (2018–2024). Feature engineering dropped 49 NaN rows → 2,930 clean rows. Train: 2,022 rows, Test: 908 rows. All cell outputs verified in notebook. |
| **Adaptations** | AI initially used a single `ColumnTransformer` instance for RF and GBR (causing a "already fitted" error in cell 12 when a second Pipeline tried to reuse the same fitted transformer). Fixed by creating separate OHE instances (`ohe2`, `ohe3`) and a fresh `prep_gbr` ColumnTransformer for GBR. Also used `sklearn.base.clone()` for Ridge's preprocessor. Specific error: `sklearn.exceptions.NotFittedError: This OneHotEncoder instance is not fitted yet` — first appeared when running cell 12 (GBR cell). |
| **Final Decision** | Used AI's scaffold with the transformer-reuse fix applied manually. The notebook structure is AI-generated; the fix to the fitted-transformer bug was identified by reading the sklearn error message and applying `clone()`. |

---

## Entry 2 — Framing decision and comparison table reasoning

| Field | Detail |
|---|---|
| **Context** | Needed to write the WHY column for the comparison table and the framing justification. Had the actual numbers from running the notebook: Grid heuristic = 3.246, Ridge = 3.299 (worse than heuristic), RF = 2.838, GBR = 2.827. |
| **Prompt** | Asked AI to draft the framing decision (why regression over binary classification) and WHY column text for each of the 6 models, using the actual measured MAE values and the RF feature importance output (grid = 37.2%, constructor_avg_grid_5 = 29.6%, rolling_avg_pts_3 = 21.0%). |
| **AI Output** | Generated framing justification noting the P1 vs P10 distinction argument, and WHY text for each model. For Ridge, correctly identified the nonlinearity problem. For GBR, explained sequential residual correction. |
| **Validation** | Checked that:  (1) The Ridge WHY column correctly states "does NOT beat the domain heuristic" — this is the actual result (3.299 > 3.246). Some AI drafts initially said Ridge "approximately matches" the heuristic (misleading framing — it is strictly worse). (2) The gap direction for Grid heuristic is negative (−0.096) — AI initially flagged this as suspicious. Confirmed this is correct: test set mean (5.13) ≈ train set mean (5.09), so no extraordinary distribution shift. (3) Feature importance percentages in comparison_table.md match the notebook cell output exactly (grid = 37.16% → 37.2%, constructor_avg_grid_5 = 29.61% → 29.6%). |
| **Adaptations** | Replaced AI's phrase "Ridge approximately matches the heuristic" with "Ridge does NOT beat the domain heuristic" to reflect the actual result honestly. Also added the negative-gap explanation paragraph to comparison_table.md (the "Key Observations" section), which AI had omitted. |
| **AI Failure** | AI's first draft of the memo.md used the phrase "our model reduces error by approximately 13%." This is accurate for RF and GBR vs. the heuristic, but misleading if a reader assumes it means vs. the simplest baseline (B1 mean). Rephrased to "13% compared to the grid look-up table" with the table providing all six numbers, so the reader can verify. |

---

## Entry 3 — memo.md non-technical language check

| Field | Detail |
|---|---|
| **Context** | Need memo to be readable by someone who understands racing but not ML. First AI draft contained phrases like "mean absolute error," "hyperparameter," and "overfitting." |
| **Prompt** | "Rewrite the memo to remove ML jargon. Replace 'MAE' with 'average prediction error (in points)'. Replace 'overfitting' with 'the models fit their training data too closely and struggle on new races'. Audience is Head of Strategy, not a data scientist." |
| **AI Output** | Generated the revised memo. Jargon mostly removed. Retained "decision trees" and "random forest" as named approaches — acceptable since the context makes them concrete. |
| **Validation** | Read the memo aloud and checked: does any sentence require ML knowledge to understand? Found "linear formula" (kept — self-explanatory), "prediction error of ±2.8 points per entry on average" (kept — concrete and interpretable). The one remaining jargon risk was "gradient boosting" — addressed by labelling it "our recommended model" in the table and explaining it iteratively corrects its own errors in the paragraph. |
| **Critical AI limitation (specific to this lab)** | AI cannot verify that numbers in the memo match the notebook output — it generates plausible-sounding numbers if not provided with the actual values. All MAE values in memo.md were manually verified against the printed table in notebook cell 24 (the `FINAL COMPARISON TABLE` output). AI draft initially wrote "average error of ±3.5 points" for the GBR model — wrong. Actual value: ±2.827 pts. Corrected to "±2.8 pts". |

---

## Summary of AI-Assisted vs. Human-Verified Components

| Component | AI role | Human verification |
|---|---|---|
| Notebook scaffold (structure, cells) | Generated | Ran all cells, confirmed outputs |
| Data loading / feature engineering code | Generated (adapted from W05 reference) | Verified row counts: 2,979 raw → 2,930 clean |
| Bug fix: transformer reuse in pipelines | AI suggested `clone()` after error description | Identified specific error message, confirmed fix worked in cell 12 |
| MAE values in all markdown files | AI used values provided by human | All numbers traced to cell 24 final table output |
| WHY column reasoning | AI drafted (using actual numbers) | Revised Ridge description to say "does NOT beat" (previous: "matches") |
| Memo jargon removal | AI rewrote | Read aloud; corrected GBR MAE from ±3.5 to ±2.8 pts |
