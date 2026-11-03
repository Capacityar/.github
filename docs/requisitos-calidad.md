# Requisitos de Calidad — Capacityar

**Elaborado por:** Scrum Master
**Fecha:** 2026-11-03
**Estado:** Vigente
**Fuente:** brecha B4.1 del análisis de brechas, métricas del `plan-de-sprints-v2.md` (§7)

---

## 1. Propósito y alcance

Este documento fija los **umbrales de calidad medibles** del proyecto. Antes de él no había requisitos de calidad escritos: no se podía medir ni auditar el producto (brecha B4.1). Define qué se exige, cómo se verifica, quién lo reporta y con qué frecuencia.

Cada requisito se estructura según una característica relevante de **ISO/IEC 25010:2023** y se vincula a su métrica del plan v2 (§7).

## 2. Cómo se usa

Cada característica tiene una tabla con:

| Campo | Significado |
|-------|-------------|
| **Umbral** | El valor medible que se debe cumplir (no negociable salvo excepción explícita) |
| **Verificación** | El método o herramienta que produce la evidencia |
| **Responsable** | Quién reporta el resultado (E1, E2, E3 o SM) |
| **Frecuencia** | Cuándo se mide |
| **Métrica v2 §7** | La fila de la tabla de métricas del plan de sprints con la que se alinea |

Un requisito sin método de verificación no es un requisito: es un deseo.

---

## 3. Requisitos por característica

### 3.1 Fiabilidad (Reliability)

Capacidad del sistema de comportarse de forma estable y previsible ante entradas y fallas.

| Umbral | Verificación | Responsable | Frecuencia | Métrica v2 §7 |
|--------|--------------|-------------|------------|---------------|
| Cobertura de tests **≥ 70 %** | `pytest --cov=app --cov-fail-under=70` en CI (`ci.yml`) | E1 | Cada sprint | *Cobertura de tests ≥ 70 %* |
| Toda falla de IA degrada a respuesta **mock** (sin excepción al usuario) | Tests de `PlanGenerator` con fallback + revisión de `docs/ai-integration.md` | E1 | Cada cambio en la integración IA | — |
| `main` nunca queda con CI en rojo | Workflow `ci` sobre `push` + reverón inmediato si se rompe | E2 | Continuo | — |

**No confundir cobertura con fiabilidad** (brecha B4.3): el 70 % de líneas ejecutadas no garantiza comportamiento correcto; es un piso mínimo, no la meta de calidad.

### 3.2 Eficiencia de desempeño (Performance efficiency)

Tiempo de respuesta y uso de recursos en condiciones esperadas.

| Umbral | Verificación | Responsable | Frecuencia | Métrica v2 §7 |
|--------|--------------|-------------|------------|---------------|
| **LCP < 2.5 s** (carga inicial) | Lighthouse / `web-vitals` sobre el deploy de Vercel | E2 | Cada sprint | *Tiempo de carga inicial < 2.5 s* |
| **P95 de recomendación < 10 s** | `scripts/load_test.py` / `k6_load_test.js` | E2 | Desde Sprint 6 | *Latencia P95 de recomendación < 10 s* |
| **Timeout de llamada a LLM = 30 s** con degradación elegante a mock | `app/shared/llm.py` + `tests/test_llm_timeout.py` | E1 | Cada cambio | — |
| Usuarios concurrentes soportados **documentados** | Informe de prueba de carga (`docs/load-testing.md`) | E2 | Sprint 6 | *Usuarios concurrentes documentado* |

### 3.3 Usabilidad (Usability)

Facilidad de uso y accesibilidad para el usuario final.

| Umbral | Verificación | Responsable | Frecuencia | Métrica v2 §7 |
|--------|--------------|-------------|------------|---------------|
| **SUS ≥ 70/100** | Encuesta SUS estandarizada post-test de usabilidad (S3.3) | E3 | Cada evaluación UX | *Puntuación SUS ≥ 70/100* |
| **WCAG 2.1 AA ≥ 95 %** de criterios cumplidos | `axe-core` / auditoría de accesibilidad en Lighthouse | E3 | Cada sprint con cambios de UI | — |

### 3.4 Seguridad (Security)

Protección de datos y de la integridad del código.

| Umbral | Verificación | Responsable | Frecuencia | Métrica v2 §7 |
|--------|--------------|-------------|------------|---------------|
| **Cero claves/secretos** en el repositorio | Escaneo `gitleaks`/`trufflehog` + revisión de `gh secret list` | E2 | Continuo (y en cada PR) | — |
| Toda credencial expuesta se **rota** (no solo se borra) | Procedimiento de rotación documentado | E2 | Al detectarse | — |
| Secrets gestionados por `gh secret` / variables de entorno, nunca en `.env` commiteado | `CONTRIBUTING.md` §5.4 | E2 | Continuo | — |

### 3.5 Safety (Seguridad de las recomendaciones de IA)

El sistema debe fallar de forma segura en el dominio educativo: una recomendación equivocada no puede presentarse como cierta.

| Umbral | Verificación | Responsable | Frecuencia | Métrica v2 §7 |
|--------|--------------|-------------|------------|---------------|
| Comportamiento **fail-safe** documentado (las recomendaciones son sugerencias, no certezas) | `docs/ai-integration.md` + `docs/ai-safety` (o equivalente) | SM | Cada cambio | — |
| El timeout y la degradación a mock no bloquean la operación | `tests/test_llm_timeout.py` | E1 | Cada cambio | — |

### 3.6 Mantenibilidad (Maintainability)

Capacidad del equipo de entender, modificar y corregir el sistema sin acumular deuda oculta.

| Umbral | Verificación | Responsable | Frecuencia | Métrica v2 §7 |
|--------|--------------|-------------|------------|---------------|
| **Cero ítems de deuda técnica sin registrar** | Todo atajo se anota en un issue con label `tech-debt` | SM | Cada sprint | *Deuda técnica documentada: 0 ítems sin registro* |
| Documentación del sprint actualizada al 100 % | Checklist del DoD (§6 del plan v2) | SM | Cada sprint | *Documentación actualizada: 100 %* |
| PRs revisados al 100 % (política ideal) o CI en verde (política vigente §0) | `CONTRIBUTING.md` §0 y §7 | SM | Cada sprint | *Pull requests revisados: 100 %* |

---

## 4. Tabla resumen de umbrales

| # | Característica | Umbral clave |
|---|----------------|--------------|
| 1 | Fiabilidad | Cobertura ≥ 70 %; fallback IA a mock; CI nunca rojo |
| 2 | Desempeño | LCP < 2.5 s; P95 < 10 s; timeout 30 s |
| 3 | Usabilidad | SUS ≥ 70; WCAG 2.1 AA ≥ 95 % |
| 4 | Seguridad | 0 secretos en repo; rotación al exponerse |
| 5 | Safety | Fail-safe documentado en recomendaciones IA |
| 6 | Mantenibilidad | 0 deuda sin registrar; docs 100 % |

---

## 5. Trazabilidad con el plan de sprints v2 (§7)

Cada umbral de este documento corresponde a una fila de la tabla de métricas del `plan-de-sprints-v2.md` §7 (columna **Métrica v2 §7** de las tablas de la sección 3). Los valores de cobertura (≥ 70 %), LCP (< 2.5 s), SUS (≥ 70), P95 (< 10 s) y deuda (0 sin registro) se toman directamente de esa tabla para no mantener dos fuentes de verdad.

Los umbrales no listados en §7 (timeout 30 s, WCAG ≥ 95 %, cero secretos, fail-safe) son requisitos derivados de las brechas B4.1/B4.3 y de la arquitectura actual; si el plan v2 los incorpora, se consolidan aquí sin duplicar.

---

## 6. Cómo se audita

1. Al cierre de cada sprint, el SM verifica que cada umbral tenga evidencia fresca o estado declarado.
2. Un umbral incumplido se registra como issue `tech-debt` con el valor medido y la acción correctiva.
3. Un umbral que el equipo considere irreal se discute en retrospectiva y se ajusta **acá**, no en el silencio.
