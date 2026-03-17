# IIT414W Lab 1 — F1 Top-10 Prediction

**Team:** [Your team name here]  
**Course:** IIT414W · Applied Machine Learning  
**Date:** March 2026

## Problem Statement

Predict whether a Formula 1 driver finishes in the **Top 10** of a race using only pre-race information.

- **Data:** 2022-2024 F1 race results from Jolpica API
- **Target:** Binary classification (Top-10 finish: Yes/No)
- **Approach:** Decision-oriented EDA + Domain heuristic baseline

---

## Quick Start (Reproduce in <10 minutes)

### Prerequisites

- Python 3.9+
- Internet connection (for Jolpica API)

### Setup

```bash
# 1. Clone the repository
git clone https://github.com/[username]/iit414w-lab01-[team_name].git
cd iit414w-lab01-[team_name]

# 2. Create and activate environment
conda env create -f environment.yml
conda activate iit414w-lab01

# Or using pip:
pip install -r requirements.txt

# 3. Run the notebooks
jupyter notebook
```

### Execution Order

1. **eda.ipynb** — Run all cells (Kernel → Restart & Run All)
   - Loads data from Jolpica API
   - Performs decision-oriented EDA
   - Creates temporal train/val/test split
   - Documents all findings

2. **baseline.ipynb** — Run all cells
   - Implements grid heuristic baseline
   - Reports accuracy on validation set (2023)
   - Provides reflection on baseline performance

### Expected Runtime

- `eda.ipynb`: ~2-3 minutes (mostly API calls)
- `baseline.ipynb`: ~1 minute

---

## Repository Structure

```
iit414w-lab01-[team_name]/
├── eda.ipynb              # Decision-oriented EDA notebook
├── baseline.ipynb         # Domain heuristic baseline
├── DATA_QUALITY_LOG.md    # Data quality issues documentation
├── PROMPTS.md             # AI usage documentation
├── README.md              # This file
├── environment.yml        # Conda environment specification
├── requirements.txt       # Pip requirements (alternative)
└── .gitignore            # Git ignore patterns
```

---

## Key Findings

### 1-3-1 Summary

**HEADLINE:**
> Grid position is a strong predictor of Top-10 finishes — a simple "start top-10 → finish top-10" heuristic achieves ~75-80% accuracy.

**EVIDENCE:**
1. Grid position shows r ≈ -0.6 correlation with finishing position
2. Top-10 rate is stable across 2022-2024 seasons (~50%)
3. Baseline significantly outperforms random guessing (50%)

**ACTION:**
> Lab 2 models must beat the 75-80% baseline to add value.

---

## Temporal Split

| Split | Season(s) | Rows | Purpose |
|-------|-----------|------|---------|
| Train | 2022 | ~440 | Learn patterns |
| Validation | 2023 | ~440 | Evaluate baseline, tune models |
| Test | 2024 | ~480 | Final evaluation only (not used in Lab 1) |

---

## Baseline Performance

| Metric | Value |
|--------|-------|
| Validation Accuracy | ~75-80% |
| Random Baseline | 50% |
| Majority Class | ~50% |
| Improvement over random | +25-30 pp |

---

## Data Sources

- **Jolpica API:** https://api.jolpi.ca/ergast/f1/
  - Successor to the deprecated Ergast API
  - Provides historical F1 race results
  - No API key required

---

## Reproducibility Checklist

- [x] RANDOM_SEED = 414 used throughout
- [x] All data fetched via API (no hardcoded CSVs)
- [x] Temporal split by season (no random splits)
- [x] Test set not accessed during development
- [x] Environment fully specified
- [x] .gitignore excludes cache and checkpoints

---

## Authors

- [Student 1 Name]
- [Student 2 Name]

---

## License

This project is for educational purposes as part of IIT414W coursework.
