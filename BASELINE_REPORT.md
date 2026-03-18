# Baseline Analysis Report — F1 Top-10 Prediction
## IIT414W Lab 1 · March 2026

---

## Executive Summary

We evaluated 15+ rule-based heuristics to predict whether an F1 driver finishes in the Top 10. The **best baseline achieves 77% accuracy** using a simple grid threshold rule.

| Metric | Value |
|--------|-------|
| Best Baseline | Grid ≤ 9 |
| Validation Accuracy | **77%** |
| Improvement over Random | +27 percentage points |
| Data | 2022-2024 F1 seasons (300 race results) |

---

## Methodology

### Data Split (Temporal)
- **Training**: 2022 season (100 results)
- **Validation**: 2023 season (100 results)
- **Test**: 2024 season (100 results) — reserved

### Leakage Prevention
All heuristics use only **pre-race features**:
- Grid position (determined by Saturday qualifying)
- Constructor (known before race)
- Driver identity (known before race)

No post-race information (points, laps, status, finishing position) was used.

---

## Baseline Results

### Top Performing Heuristics

| Rank | Heuristic | Accuracy | Description |
|------|-----------|----------|-------------|
| 1 | **Grid ≤ 9** | **77%** | Predict Top-10 if starting position ≤ 9 |
| 2 | Grid ≤ 8 | 76% | Predict Top-10 if starting position ≤ 8 |
| 3 | Grid ≤ 10 OR Top-4 Team | 76% | Grid ≤ 10 OR with Red Bull/Ferrari/Mercedes/McLaren |
| 4 | Grid ≤ 9 OR Elite Driver | 76% | Grid ≤ 9 OR elite driver (Verstappen, etc.) |
| 5 | Grid ≤ 12, Not Bottom Team | 75% | Grid ≤ 12 AND not with Williams |

### Comparison Baselines

| Baseline | Accuracy |
|----------|----------|
| Grid ≤ 9 (Best) | 77% |
| Grid ≤ 10 (Original) | 74% |
| Majority Class | 50% |
| Random Guess | 50% |

### Driver-Specific Heuristics

| Heuristic | Accuracy | Notes |
|-----------|----------|-------|
| Elite Drivers Only | 60% | Verstappen, Leclerc, Sainz, Pérez |
| Top-4 Teams | 68% | Red Bull, Ferrari, Mercedes, McLaren |

**Key Insight**: Driver-specific rules underperform because elite drivers typically qualify in the top positions anyway — the grid threshold already captures them.

---

## Key Findings

### 1. Grid Position is the Strongest Predictor
- Pearson correlation with Top-10 finish: r = -0.56 (strong)
- Grid ≤ 9 captures most Top-10 finishers while avoiding false positives

### 2. Optimal Threshold is 9, Not 10
- Grid ≤ 10 would "perfectly" predict Top-10 if no position changes occurred
- In practice, ~1 driver from outside Top 10 finishes in points due to DNFs/overtakes
- Being conservative (≤ 9) improves accuracy by 3 percentage points

### 3. Team/Driver Features Add Little Value
- Constructor-based rules: 66-68% accuracy
- Combining grid + team rarely beats grid alone
- Elite drivers (Verstappen, etc.) already qualify in top positions

### 4. Class Balance is Favorable
- Top-10: 50.3% | Non-Top-10: 49.7%
- Accuracy is a meaningful metric (unlike imbalanced datasets)

---

## Threshold for Lab 2

> **Any machine learning model must beat 77% accuracy on the validation set to add value over this simple heuristic.**

If an ML model achieves similar or lower accuracy with more complexity, the Grid ≤ 9 heuristic should be preferred due to:
- Simplicity and interpretability
- No training required
- Robustness to overfitting
- Easy explanation to stakeholders

---

## Recommendations

1. **Use Grid ≤ 9 as the production baseline** until an ML model demonstrates clear improvement
2. **Focus Lab 2 on features beyond grid position** — grid alone captures most signal
3. **Consider DNF prediction** as a separate task to improve overall accuracy
4. **Evaluate precision/recall** in addition to accuracy for operational decisions

---

## Files

| File | Description |
|------|-------------|
| `eda.ipynb` | Exploratory Data Analysis notebook |
| `baseline.ipynb` | Baseline heuristic evaluation |
| `BASELINE_REPORT.md` | This report |

---

*Report generated: March 18, 2026*
