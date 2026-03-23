# Lab 2: Feature Engineering + Improved Baseline
## IIT414W · David Hernandez y Ariel Van Kilsdonk

### Objetivo
Superar los baselines de Lab 1 usando features pre-race y un modelo simple (Logistic Regression).

### Estructura
```
lab_02/
├── lab02_feature_engineering.ipynb  # Notebook principal
├── comparison_table.md              # Resultados Lab 1 vs Lab 2
├── PROMPTS.md                       # Documentación de uso de IA
└── README.md                        # Este archivo
```

### Cómo reproducir

1. **Requisitos:**
   ```bash
   pip install numpy pandas scikit-learn requests
   ```

2. **Ejecutar notebook:**
   - Abrir `lab02_feature_engineering.ipynb` en Jupyter/VS Code
   - Ejecutar todas las celdas de arriba hacia abajo
   - Tiempo estimado: ~5 minutos

3. **Seed de reproducibilidad:**
   ```python
   RANDOM_SEED = 414
   ```

### Features implementadas

| Feature | Tipo | Descripción |
|---------|------|-------------|
| `prev_position` | Lag | Posición del piloto en carrera anterior |
| `avg_position_last3` | Rolling | Promedio de posición en últimas 3 carreras |
| `driver_circuit_avg_prev` | Interaction | Promedio histórico del piloto en ese circuito |
| `constructor_tier_prev_season` | Categorical | Tier del constructor (top/mid/back) según temporada anterior |

### Resultados

| Modelo | Accuracy |
|--------|----------|
| Majority class | 50% |
| Domain heuristic (grid ≤ 10) | **74%** |
| Lab 2 LogReg | 73% |

**El modelo NO superó el domain heuristic.** Ver `comparison_table.md` para interpretación completa.

### Validación temporal
- **Train:** 2022 (100 resultados)
- **Validation:** 2023 (100 resultados)
- **Test:** 2024 (100 resultados) — SEALED, no usado

### Leakage checklist
Todas las features usan solo información pre-race. Ver celda "Leakage Guard Checklist" en el notebook.
