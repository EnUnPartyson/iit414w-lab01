# Technical Memo — F1 Points Prediction Model
**To:** Head of Strategy, Scuderia [Team]  
**From:** Data Science, IIT414W Lab 3  
**Date:** April 20, 2026  
**Subject:** Can machine learning reliably predict how many points a driver will score?

---

## What We Built

We trained and tested six prediction systems on seven seasons of Formula 1 race results (2018–2024). Each system takes information available *before* a race — starting grid position, the driver's recent finishing history, and the constructor's recent pace — and produces a single number: how many points that driver is expected to score on Sunday.

We trained each system on races from 2018 to 2022, and tested it exclusively on the 2023 and 2024 seasons. This ensures we are measuring how the system would perform in a genuine deployment scenario, not on races it has already seen.

---

## What We Found

A simple look-up table — "on average, a driver starting P3 scores X points" — turned out to be a strong, hard-to-beat baseline. Here is how all six approaches performed on the 2023–2024 test seasons, ranked from worst to best:

| Approach | Average prediction error (points) | What it does |
|---|---|---|
| Always predict the seasonal average | ±5.9 pts | Ignores all race-day information |
| Always predict zero | ±5.1 pts | Half of entries score zero, so this is often right — but useless when it isn't |
| Grid look-up table | ±3.2 pts | Maps starting position to historical average points |
| Ridge regression (linear model) | ±3.3 pts | Combines grid, driver form, team pace — linear formula |
| Random forest (100 decision trees) | ±2.8 pts | Learns non-obvious patterns from historical data |
| **Gradient boosting (our recommended model)** | **±2.8 pts** | Iteratively corrects its own errors; best overall performance |

"Average prediction error" here means that on a race weekend with 20 drivers, the model's point estimates are off by 2.8 points per driver on average. For a midfield entry (expecting 2–4 points) that is a significant margin of uncertainty; for race-winner predictions, it is reasonable.

---

## What the Machine Learning Models Add

The two most sophisticated models — random forest and gradient boosting — reduce the prediction error by approximately **13% compared to the grid look-up table**. Why? The look-up table only considers where a driver starts. The machine learning models additionally use:

- **How the driver has been finishing recently** (a driver on a streak of top-5 results tends to continue)
- **The pace of the constructor's car** (a team that has been qualifying in P5–P7 consistently is unlikely to suddenly win)

Strikingly, a standard linear formula (Ridge regression) did *not* improve on the look-up table. This is because the relationship between grid position and points is not smooth: a jump from P1 to P2 costs 7 points, while a jump from P9 to P10 costs just 1 point. A linear model cannot express this unevenness without additional engineering.

---

## Confidence and Limitations

**What the model can do reliably:**  
Identify which drivers are structurally likely to score (front-row qualifiers in strong constructors) versus likely to score zero (midfield grid positions in slower teams), on the basis of historical patterns.

**What the model cannot do:**  
Predict race-day surprises. Safety cars, rain, first-lap incidents, tyre strategy variations, and mechanical failures are absent from the data. Roughly half of all race entries score zero points; the model predicts these reasonably well on average, but individual zero-point results for strong drivers (e.g., a race leader retiring from the lead) cannot be anticipated.

**Recommended use:**  
Season-level points accumulation estimates — e.g., "over the remaining 8 races, how many points should we expect from Driver A vs. Driver B?" — not race-by-race certainties. The model should be one input among many, alongside live tyre data and circuit-specific knowledge.

---

## Recommendation

Deploy the gradient boosting model for strategic planning at the season-aggregate level. Report its estimates with a stated uncertainty of ±3 points per race entry, and treat any prediction for a front-row qualifier in a top-3 constructor as the narrowest-uncertainty scenario. Do not rely on the model for in-race strategy decisions where live information (weather, gaps, tyre wear) dominates.
