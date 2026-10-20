# Métricas de Ingeniería de Software — Análisis para IRIS-CapaciTar

**Fecha de elaboración:** 2026-10-20
**Elaborado por:** Scrum Master
**Propósito:** responder el cuestionario de métricas y servir de insumo para actualizar el documento de Ingeniería de Requerimientos (issue S1.6).

> **Nota sobre nomenclaturas:** los códigos `RF-XXX`, `MET-XXX`, `RNF-X`, `TUT-XXX` provienen de un documento externo y **no pertenecen** a nuestro `requerimientos-v2.md`. Se usan aquí solo como referencia para mapear los conceptos que hay que incorporar en la actualización del DOC. Cada punto indica si el concepto ya está en nuestro material (✅), está parcial (⚠️) o falta (❌).

---

## Pregunta 1 — Marco Conceptual (Monitoreo Estratégico / Inserción Laboral)

### a) Medida directa, métrica derivada e indicador de impacto

- **Medida directa:** número de postulantes contratados por una PYME en un período (dato bruto contable).
- **Métrica derivada:** **tasa de inserción laboral** = (postulantes contratados ÷ postulantes totales evaluados) × 100. Combina dos medidas directas.
- **Indicador de impacto:** **retención/desempeño a 90 días** reportado por la PYME (o % de PYMES que renuevan el uso de la plataforma). Mide el *efecto* del programa, no solo el output.

> Estado: ⚠️ **Parcial.** El concepto de "medida vs. métrica vs. indicador" está desarrollado en la Unidad IV (brechas), pero **no formalizado** en `requerimientos-v2.md` con estas tres categorías.

### b) Fórmula de porcentaje de "Match" (MET-004)

- **Match (%)** = (habilidades que el postulante cumple ÷ habilidades requeridas por el puesto) × 100. Idealmente **ponderado** por la criticidad de cada habilidad.
- Para convertir el dato bruto (ej. "6 de 10 habilidades") en **indicador de empleabilidad**: normalizar a porcentaje (60 %) y **categorizar** en bandas de decisión:
  - ≥ 80 % → alta empleabilidad (avanza a entrevista)
  - 60–79 % → media (requiere capacitación, plan 5P)
  - < 60 % → baja (gap crítico)

> Estado: ⚠️ **Parcial.** El cálculo de Match existe en código (`app/modules/gap_analysis/` y `attempts/service.py` con `MASTERY_THRESHOLD = 0.80`), pero la **fórmula normalizada + bandas** no está documentada.

---

## Pregunta 2 — Métricas de Requerimientos (Volatilidad / RAG)

### a) Tasa de Volatilidad de Requerimientos (MET-002)

- **Volatilidad** = (requisitos agregados + modificados + eliminados en el sprint ÷ requisitos totales al inicio del sprint) × 100.
- Para las tablas de scoring: se cuenta cada **campo** de requerimiento (habilidad, puesto, seniority) que cambió respecto de la línea base.

> Estado: ❌ **No está.** No existe en `requerimientos-v2.md` ni en el plan de sprints.

### b) Indicador de riesgo por inestabilidad del RAG (RF-IA-004)

- Si los campos de requerimientos cambian continuamente durante el sprint, la base de conocimiento del RAG (embeddings/índice) queda **obsoleta**.
- **Indicador de riesgo técnico:** **"Staleness del índice RAG"** = días desde la última re-indexación vs. frecuencia de cambios de campos. Regla de alerta: si la volatilidad por sprint supera un umbral (p. ej. > 15 %) o hay campos modificados sin re-indexar, se dispara alerta de **re-indexación obligatoria**.

> Estado: ❌ **No está.** Relacionado con la brecha B4.7/B5.x (seguridad/safety de la IA).

---

## Pregunta 3 — Métricas de Diseño Webapps (Agente Tutor responsive)

### a) Métricas de rendimiento y usabilidad en móvil

**Rendimiento:**
- **TTFT** (tiempo hasta el primer token) y **latencia P95/P99** de la respuesta del LLM.
- **LCP** (carga inicial) y rendimiento en red móvil (3G/4G).

**Usabilidad:**
- **SUS** (≥ 70/100), tasa de éxito de tarea, tasa de abandono de sesión, satisfacción (CSAT).

> Estado: ⚠️ **Parcial.** Ya tenemos en el plan de sprints: `SUS ≥ 70`, `LCP < 2.5 s`, `latencia P95 < 10 s`. **Falta:** TTFT y métricas específicas de red móvil.

### b) Métrica indirecta de Eficiencia de Costos por Token (RAG)

- **Costo por token** = costo total API ÷ tokens totales (prompt + completion).
- **Métrica indirecta:** **costo por respuesta útil** = (costo total del período) ÷ (consultas resueltas satisfactoriamente). Se puede derivar sin instrumentar la facturación: medir `tokens/consulta` × `precio por 1M tokens` del proveedor.

> Estado: ❌ **No está.** Relacionado con el costo de la calidad (Unidad IV) y el riesgo de "cuotas limitadas de LLM gratuitas" (ya identificado en el plan).

---

## Pregunta 4 — Métricas para Pruebas (Densidad de Defectos / Cobertura Match)

### a) Densidad de Defectos

- **Python:** densidad = defectos ÷ KLOC (miles de líneas de código).
- **n8n (No-Code):** densidad = defectos ÷ número de **nodos** (o workflows). Es la adaptación de KLOC a flujos visuales.

> Estado: ❌ **No está.** No hay métrica de densidad de defectos (solo "issues abiertos/cerrados" en el plan).

### b) Cobertura de pruebas para evitar falsos positivos en Match

- La cobertura de sentencias (≥ 70 %) **no basta**. Para evitar falsos positivos en el cálculo de Match se necesita:
  - **Cobertura de condiciones/decisión** (branch/condition coverage) sobre las reglas de matching.
  - **Partición de equivalencia + valores límite** en los umbrales (ej. el 80 % de mastery).
  - Idealmente **mutation testing** para verificar que los tests detectan cambios en la lógica.
  - Como métrica de calidad: **precisión (precision)** = verdaderos positivos ÷ (verdaderos positivos + falsos positivos).

> Estado: ⚠️ **Parcial.** Tenemos `cobertura ≥ 70 %` (S2.2/S2.3), pero **no** cobertura de decisión, ni mutation testing, ni métrica de precisión del Match.

---

## Pregunta 5 — Métricas para Mantenimiento (MTTR / Grounding RAG)

### a) MTTR ante caída de una API de LLM de terceros

- **MTTR** = Σ(tiempo de reparación de cada incidente) ÷ número de incidentes.
- Para una API externa (OpenAI): MTTR = tiempo desde la **detección** de la caída hasta el **restablecimiento del servicio** vía failover (cambio a otro proveedor, Groq/Gemini/mock). Incluye: detección (monitoreo) + failover automático + recuperación.

> Estado: ⚠️ **Parcial.** El backend ya tiene **fallback automático a Mock/Groq/Gemini** (`app/shared/llm.py`, épica C4), pero el **MTTR no está medido** como métrica.

### b) Métrica de grounding/precisión del RAG (RF-IA-004)

- **Grounding (fidelity/faithfulness)** = proporción de afirmaciones de la respuesta que están **respaldadas por las fuentes recuperadas** (sin alucinaciones).
- Complementarias: **precisión/recall del retriever** (qué tan bien recupera el chunk correcto) y **tasa de alucinación**.
- Se mide con evaluación humana (el Tutor revisa) o automática (framework RAGAS: `faithfulness`, `answer relevancy`, `context precision`).

> Estado: ⚠️ **Parcial.** Tenemos **anti-alucinación de URLs** (épica C6, whitelist de dominios), pero **no** una métrica formal de grounding/precisión del RAG. La brecha B4.7 (safety de la IA) lo señala.

---

## Resumen: qué incorporar en la actualización del DOC (S1.6)

| Concepto | Estado |
|----------|--------|
| Medida directa / derivada / indicador | ⚠️ Parcial (solo en Unidad IV) |
| Fórmula de Match + bandas de empleabilidad | ⚠️ Parcial (código, sin doc) |
| Tasa de volatilidad de requerimientos | ❌ Falta |
| Staleness/riesgo del índice RAG | ❌ Falta |
| TTFT y métricas móviles | ⚠️ Parcial (falta TTFT) |
| Costo por token / costo por respuesta | ❌ Falta |
| Densidad de defectos (KLOC / nodos) | ❌ Falta |
| Cobertura de decisión + precisión del Match | ⚠️ Parcial (falta) |
| MTTR de failover LLM | ⚠️ Parcial (falta medición) |
| Grounding/precisión del RAG | ⚠️ Parcial (falta métrica) |

---

## Referencia cruzada con issues

- **S1.6** (actualizar requerimientos-v2.md): incorporar las métricas faltantes (❌) y formalizar las parciales (⚠️).
- **S2.2/S2.3** (tests): cobertura de decisión + precisión del Match.
- **S5.x** (calidad/SQA): requisitos de calidad por característica, densidad de defectos.
- **S6.x** (tiempo real): TTFT, P95/P99, MTTR de failover.
