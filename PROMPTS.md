# AI Usage Documentation — Lab 1

## Overview
This document records all significant AI tool usage during the development of Lab 1 for IIT414W. Following the 6-field format required by the course.

---

## Entry 1 — March 16, 2026 — Data Loading Pattern from Jolpica API

**Context:** 
I needed to fetch F1 race results from the Jolpica API and parse the nested JSON structure into a flat DataFrame suitable for analysis.

**Prompt(s):**
> "Show me how to fetch F1 race results from the Jolpica API (Ergast successor) for multiple seasons and flatten the nested JSON into a pandas DataFrame with one row per driver per race."

**Output:**
The AI provided a code pattern using `requests.get()` to fetch data from `https://api.jolpi.ca/ergast/f1/{year}/results.json?limit=1000`, then iterating through the nested `MRData > RaceTable > Races > Results` structure to extract driver, constructor, grid, and position information.

**Validation:**
- Verified the API endpoint works by testing with 2024 season data
- Confirmed the DataFrame shape matches expected values (~440 rows per 22-race season with 20 drivers)
- Cross-referenced sample results with official F1 records

**Adaptations:**
- Added timeout parameter to prevent hanging on API issues
- Added print statements to show loading progress per season
- Included additional fields (circuit_id, date, fastest_lap_rank) beyond the basic example

**Final Decision:**
Used — The pattern worked correctly and followed the established W01 course pattern.

---

## Entry 2 — March 16, 2026 — Understanding MCAR/MAR/MNAR Classification

**Context:**
I needed to classify missing values in the dataset according to the MCAR/MAR/MNAR framework for the data quality audit.

**Prompt(s):**
> "Explain the difference between MCAR, MAR, and MNAR missing data mechanisms. Give examples for F1 race data where fastest_lap_time might be missing."

**Output:**
The AI explained:
- MCAR (Missing Completely At Random): Missingness unrelated to any data
- MAR (Missing At Random): Missingness can be predicted from observed data
- MNAR (Missing Not At Random): Missingness depends on the unobserved value itself

For fastest_lap_time, it classified as MNAR because drivers who DNF early have no fastest lap — the missing value depends on the (unobserved) fact that they couldn't complete laps.

**Validation:**
- Cross-referenced with course material from Week 2 on data quality
- Verified the classification logic makes sense for F1 context
- Checked that our treatment (flag, don't impute) aligns with MNAR best practices

**Adaptations:**
- Applied the same reasoning to fastest_lap_rank
- Determined position_str 'R' values are MAR (predictable from status column)

**Final Decision:**
Used — The classification framework helped structure the DATA_QUALITY_LOG.md documentation.

---

## Entry 3 — March 16, 2026 — Grid Heuristic Baseline Logic

**Context:**
I wanted to confirm my domain heuristic (grid ≤ 10 predicts Top-10 finish) was a reasonable approach before implementing it.

**Prompt(s):**
> "In F1, is it reasonable to assume that drivers starting in the top 10 grid positions are likely to finish in the top 10? What factors might make this assumption fail?"

**Output:**
The AI confirmed this is a reasonable domain heuristic because:
- Track position matters significantly in F1 (overtaking is difficult)
- Qualifying performance correlates with race pace
- Modern F1 has relatively low variance for top teams

It noted failure cases:
- DNFs due to mechanical issues
- Strategic mistakes or incidents
- Weather changes affecting car performance
- First-lap collisions

**Validation:**
- Computed actual correlation between grid and finishing position (~0.6 correlation)
- Verified baseline accuracy (~75-80%) is substantially better than random (50%)
- Checked that errors align with identified failure modes (DNFs, incidents)

**Adaptations:**
- Documented failure modes in the reflection section
- Noted that ~20-25% error rate is expected given these real-world factors

**Final Decision:**
Used — The domain reasoning validated my approach. The heuristic performs as expected.

---

## Entry 4 — March 16, 2026 — Survivorship Bias Check Approach

**Context:**
The assignment requires checking for cognitive traps. I wanted to ensure my survivorship bias analysis was rigorous.

**Prompt(s):**
> "How would you check for survivorship bias in a dataset of F1 race results? What would indicate that the analysis is biased toward 'successful' cases?"

**Output:**
The AI suggested comparing:
1. Distribution of starting positions for finishers vs. non-finishers
2. DNF rates by grid position
3. Whether excluding non-finishers would change conclusions

It noted survivorship bias would be present if:
- Analysis only used finishers (ignoring DNFs)
- Conclusions assumed all starters could be evaluated equally

**Validation:**
- Implemented the suggested comparisons in the notebook
- Verified our dataset includes ALL starters, not just finishers
- Confirmed DNF rate is roughly uniform across grid positions

**Adaptations:**
- Added Mann-Whitney U test to quantify grid position differences
- Documented that non-finishers are correctly assigned top10=0

**Final Decision:**
Used — The approach helped structure a rigorous survivorship bias check.

---

## Summary

| Entry | Purpose | Decision |
|-------|---------|----------|
| 1 | API data loading | Used |
| 2 | MCAR/MAR/MNAR classification | Used |
| 3 | Domain heuristic validation | Used |
| 4 | Survivorship bias methodology | Used |

All AI outputs were validated against course materials, empirical testing, or domain knowledge before use.

---

*Note: Basic IDE autocomplete (variable names, function signatures) was also used throughout but not documented as this is standard development practice.*
