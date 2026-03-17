# Data Quality Log — Lab 1

## Overview
This log documents all data quality issues discovered during EDA and baseline development for the F1 Top-10 prediction task using 2022-2024 season data from the Jolpica API.

---

## Issue 1: fastest_lap_rank — Missing Values (MNAR)

- **What:** The `fastest_lap_rank` column has missing values for drivers who did not complete any laps (DNF/DNS on lap 1, etc.)
- **Classification:** MNAR (Missing Not At Random)
- **Impact:** Cannot compute fastest lap statistics for these drivers. Missing values are informative — drivers who DNF early have no fastest lap.
- **Decision:** Flag as separate category; do not impute.
- **Justification:** Imputing a fastest lap time for drivers who didn't complete laps would be misleading. The missingness itself is informative.

---

## Issue 2: fastest_lap_time — Missing Values (MNAR)

- **What:** The `fastest_lap_time` column is missing for drivers with no recorded fastest lap
- **Classification:** MNAR (Missing Not At Random)
- **Impact:** Same as fastest_lap_rank — cannot compute lap time features for DNF drivers
- **Decision:** Flag as separate category; do not impute.
- **Justification:** Same reasoning as Issue 1. The absence of a fastest lap time indicates the driver did not complete meaningful laps.

---

## Issue 3: position_str — Non-Numeric Values ('R')

- **What:** The `position_str` column contains 'R' for retired/non-classified drivers instead of numeric positions
- **Classification:** MAR (Missing At Random) — missingness is predictable from `status` column
- **Impact:** Cannot directly use position_str for numeric operations
- **Decision:** Create numeric `position` column with NaN for non-finishers; assign top10=0 for these cases
- **Justification:** Non-finishers definitely did not finish in the Top 10, so they should be classified as non-Top-10 outcomes.

---

## Issue 4: grid — Zero Values (Pit Lane Starts)

- **What:** Grid position = 0 indicates pit lane starts (drivers excluded from qualifying for various reasons)
- **Classification:** Not missing — this is valid data representing a special case
- **Impact:** Grid = 0 is worse than grid = 20 in terms of starting position
- **Decision:** Keep as-is; the heuristic (grid ≤ 10) naturally handles this correctly (predicts non-Top-10)
- **Justification:** Pit lane starts are rare but valid. The heuristic correctly predicts these drivers won't finish Top 10.

---

## Issue 5: driver_code — 'N/A' Values

- **What:** Some drivers have 'N/A' as their driver code (typically reserve/substitute drivers)
- **Classification:** MAR (Missing At Random) — depends on driver's official status
- **Impact:** Cannot use driver_code as a feature for these drivers
- **Decision:** Keep 'N/A' as a valid category; use driver_id instead for unique identification
- **Justification:** driver_code is not used in our baseline; driver_id is always populated.

---

## Issue 6: Temporal Consistency — Race Calendar Variations

- **What:** Number of races varies by season (22-24 races per season)
- **Classification:** Not a defect — reflects actual F1 calendar changes
- **Impact:** Slightly different sample sizes per season
- **Decision:** Keep all data; document the variation
- **Justification:** All races are valid data points regardless of calendar length.

---

## Issue 7: DNF Rates — Potential Outlier Races

- **What:** Some races have unusually high DNF rates (wet races, first-lap incidents)
- **Classification:** Not a defect — reflects actual race conditions
- **Impact:** May cause prediction errors for the grid heuristic (drivers starting top-10 but DNFing)
- **Decision:** Keep all data; acknowledge this as a limitation of the baseline
- **Justification:** High-DNF races are part of realistic F1 predictions. The baseline's ~20-25% error rate partially reflects this unpredictability.

---

## Summary Statistics

| Issue | Classification | Action Taken |
|-------|---------------|--------------|
| fastest_lap_rank missing | MNAR | Flag, don't impute |
| fastest_lap_time missing | MNAR | Flag, don't impute |
| position_str 'R' values | MAR | Convert to NaN, set top10=0 |
| grid = 0 (pit lane) | Valid | Keep as-is |
| driver_code 'N/A' | MAR | Use driver_id instead |
| Race calendar variation | Valid | Document only |
| High-DNF races | Valid | Document as limitation |

---

*Last updated: Lab 1 submission*
