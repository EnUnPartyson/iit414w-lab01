# Lab 1 vs Lab 2 — Comparison Table
## David Hernandez y Ariel Van Kilsdonk

| Model / Baseline | Accuracy | Precision | Recall | F1 | ROC-AUC |
|------------------------|----------|-----------|--------|-------|---------|
| Majority class (Lab 1) | 0.50 | 0.00 | 0.00 | 0.00 | N/A |
| Domain heuristic (Lab 1)| 0.74 | 0.74 | 0.74 | 0.74 | N/A |
| Prior-period (if done) | — | — | — | — | — |
| Lab 2 model (LogReg) | 0.73 | 0.78 | 0.64 | 0.70 | 0.78 |

## Primary metric: Accuracy (justified in Lab 1)

## Interpretation

El modelo de Lab 2 **no superó el baseline del domain heuristic** en accuracy (73% vs 74%), aunque sí mejoró en precision (78% vs 74%). Esto indica que cuando el modelo predice Top-10, es más confiable que la heurística, pero a costa de menor recall (64% vs 74%) — es decir, deja más casos genuinos sin predecir.

El domain heuristic (grid ≤ 10) fue el baseline más difícil de superar porque captura una relación casi perfecta: si un piloto clasifica en las primeras 10 posiciones, históricamente tiene alta probabilidad de terminar en Top-10. Las features adicionales (momentum previo, promedio rolling, afinidad circuito-piloto, tier de constructor) no aportaron información suficientemente ortogonal a la posición de grilla.

**Conclusión:** Las features temporales agregan complejidad sin mejorar el resultado principal. El domain heuristic sigue siendo la mejor opción para predecir Top-10 con accuracy como métrica. Para Lab 3, se podría explorar: (1) features de confiabilidad/DNF, (2) interacciones constructor×circuito, (3) condiciones meteorológicas históricas.
