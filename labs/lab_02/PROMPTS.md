# PROMPTS.md — Documentación de Uso de IA
## Lab 2: Feature Engineering + Improved Baseline
## David Hernandez y Ariel Van Kilsdonk

---

### Prompt 1: Ejecución y validación del notebook

**Context:** Notebook de Lab 2 con features y modelo implementados, pero sin ejecutar.

**Prompt:** "i have to do the lab 2 right now, this is the lab, and this are the instructions [instrucciones del lab]"

**Output:** El asistente ejecutó todas las celdas del notebook secuencialmente, corrigió un error en el código de error analysis (problema con pandas groupby columns), y generó los archivos requeridos (comparison_table.md, README.md).

**Validation:** 
- Verificamos que todas las celdas ejecutaron sin errores
- Confirmamos que los resultados son reproducibles con RANDOM_SEED = 414
- Validamos que el modelo no superó el baseline (73% vs 74%)

**Adaptations:** 
- Se corrigió el error en la celda de error analysis cambiando el método de groupby para evitar el problema de columnas MultiIndex
- Se actualizó la interpretación en comparison_table.md para reflejar honestamente que el modelo no superó el baseline

**Final Decision:** Se aceptó el resultado del modelo (73% accuracy) como válido. Aunque no supera el baseline, el lab indica que esto es un resultado válido si se explica honestamente por qué y qué se intentaría a continuación.

---

### Prompt 2: Corrección de error en pandas groupby

**Context:** Error `ValueError: Length mismatch` al ejecutar la celda de error analysis.

**Prompt:** (Implícito al continuar ejecución)

**Output:** Se identificó que `as_index=False` combinado con `.agg(['mean', 'count'])` produce columnas inesperadas. Se cambió a usar groupby sin `as_index=False` y luego `.reset_index()`.

**Validation:** La celda ejecutó correctamente mostrando:
- Top 3 carreras por error rate: Bahrain GP (45%), Australian GP (45%), Saudi Arabian GP (25%)
- Top 3 pilotos por error rate: Norris (60%), Albon (40%), Gasly (40%)
- 18 False Negatives, 9 False Positives

**Adaptations:** Ninguna adicional.

**Final Decision:** Se aplicó la corrección directamente al notebook.

---

### Notas adicionales

- El notebook fue desarrollado principalmente por los estudiantes
- La IA asistió en la ejecución, debugging, y generación de archivos de documentación
- Todos los resultados fueron verificados manualmente antes de su aceptación
