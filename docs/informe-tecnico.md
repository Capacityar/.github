# Informe de estado — Capacityar (Sprint 2)

Estado del proyecto **Capacityar** al cierre del Sprint 2 (noviembre 2026): qué se construyó, cuáles son las fortalezas, qué falta de forma honesta, y las métricas que lo respaldan.

> Este informe reemplaza el "Informe técnico C1" anterior. Las cifras provienen de los repositorios y del project board de la organización al momento de la redacción.

---

## 1. Resumen ejecutivo

Capacityar es un **Sistema de Tutoría Inteligente (ITS)** que capacita a jóvenes de barrios vulnerables de Argentina para insertarse en el sector IT, articulado sobre el modelo pedagógico de las **5 P** (Pasión, Play, Práctica, Paciencia, Perseverancia) con esquema **Human-in-the-Loop (HITL)**. Proyecto académico de *Ingeniería de Software II* (UMET), equipo de 9 integrantes + docente.

El **núcleo del producto (épicas C1–C6) está completo y desplegado**: backend FastAPI en Render, frontend React en Vercel, IA híbrida con fallback, y calidad de base (CI, tests, Alembic, CORS) ya resuelta. Lo que falta es la **segunda capa de funcionalidad** (tutores, alertas, perseverancia, validaciones) y sobre todo la **madurez del frontend** (0 % de tests, datos mockeados, accesibilidad sin medir).

**Métrica clave:** 127 issues totales, 77 cerrados (**60.6 %**). Cobertura backend **73 %**, frontend **0 %**.

---

## 2. Lo realizado

### 2.1 Núcleo MVP (épicas C1–C6) — completo

| Épica | Módulo / funcionalidad | Estado |
|-------|------------------------|--------|
| C1 | `users` + `skills` + `user_skills` + `job_descriptions` (CRUD con roles) | ✅ |
| C2 | `learning_paths` (generación del plan a partir del gap) | ✅ |
| C3 | Persistencia PostgreSQL (Neon) + deploy Render | ✅ |
| C4 | IA híbrida: Groq Llama + Gemini + fallback Mock | ✅ |
| C5 | `attempts` + feedback empático (Paciencia, 4.ª P) | ✅ |
| C6 | `resources` con anti-alucinación de URLs | ✅ |

Además del módulo `gap_analysis` (diagnóstico de brecha por documentos).

### 2.2 Calidad de base (Épica 8) — completo

- **Tests automatizados**: `pytest` + `httpx`, cobertura **73 %** (umbral CI ≥ 70 %).
- **Alembic**: migraciones versionadas (`alembic/versions/`).
- **CORS**: habilitado para el origen de Vercel.
- **CI**: `ci.yml` con `ruff` (lint) + `pytest --cov-fail-under=70`.

### 2.3 Autenticación (Épica 6) — mock + JWT

- Headers mock (`X-User-Id` / `X-User-Role`) para el MVP.
- JWT real implementado (S2.1): `POST /auth/register`, `/login`, `/refresh`, `/logout`.
- `AUTH_MODE=mock|jwt` para alternar sin redeploy.

### 2.4 Frontend

- React 19 + Vite + TypeScript + Tailwind CSS v4.
- ~15 páginas con routing (Wouter), TanStack Query, shadcn/ui.
- Deploy estático en Vercel (SPA con rewrite a `index.html`), desacoplado del backend.

### 2.5 Proceso y documentación

- [CONTRIBUTING.md](../CONTRIBUTING.md): política de PR/revisión, convención de commits, versionado semántico.
- [requisitos-calidad.md](requisitos-calidad.md): umbrales medibles por característica (ISO/IEC 25010).
- [metricas-ingenieria-software.md](metricas-ingenieria-software.md), [plan-de-sprints-v2.md](plan-de-sprints-v2.md), [manual-de-usuario.md](manual-de-usuario.md), [tech-stack.md](tech-stack.md).

---

## 3. Fortalezas

1. **Arquitectura modular limpia**: cada capacidad sigue `router → service → repository` con modelos/schemas separados. Los proveedores externos (LLM, repositorios) se inyectan por **Protocol + Factory**, lo que permite cambiar Groq ↔ Gemini ↔ Mock sin tocar el resto.
2. **IA híbrida fail-safe**: el código determinístico fija la estructura del plan y el LLM solo rellena contenido; ante falla del LLM el sistema degrada a Mock en lugar de devolver un 500. Timeout explícito de 30 s.
3. **Anti-alucinación de URLs**: whitelist de dominios oficiales (MDN, react.dev, git-scm…) y URLs de búsqueda de YouTube en lugar de links inventados.
4. **Calidad con evidencia**: CI en verde con cobertura medible (≥ 70 %), migraciones versionadas, CORS tipificado (sin wildcard en producción).
5. **Proceso formalizado**: política de revisión con independencia de roles, checklist de PR, versionado semántico, requisitos de calidad auditables. Nada de esto existía en el inicio del proyecto.
6. **Despliegue desacoplado**: backend y frontend se despliegan y escalan por separado (Render / Vercel), eliminando el acoplamiento del MVP inicial.

---

## 4. Lo que falta (de forma honesta)

### 4.1 Funcionalidad (épicas pendientes)

| Épica | Qué falta | Estado |
|-------|-----------|--------|
| Épica 1 | `sessions` + `student_metrics` (Perseverancia, 5.ª P) | ⚠️ Pendiente |
| Épica 2 | CRUD de tutores, asignaciones, dashboard (HITL real) | ⚠️ Pendiente |
| Épica 3 | Sistema de alertas predictivas | ⚠️ Pendiente |
| Épica 4 | `next-unit` y desbloqueo progresivo | ⚠️ Pendiente |
| Épica 5 | Validaciones críticas (juicio humano) | ⚠️ Pendiente |
| Épica 7 | Eventos hacia Equipo 1 (métricas globales) | ⚠️ Pendiente |

La **matriz de accesos** (`access-matrix.md`) está definida pero no aplicada de punta a punta.

### 4.2 Frontend (el mayor gap)

- **0 % de cobertura de tests** y **sin CI** en el repo `frontend` (a diferencia del backend).
- **Datos mockeados**: Dashboard, Evaluación, CanalTutor y otras pantallas muestran datos fijos, no los de la API (issues S4.1–S4.4).
- **Enlaces rotos** y **página 404 en inglés** (hallazgos H01/H02 de la evaluación heurística).
- **Accesibilidad y responsive sin cumplir**: sidebar de 240 px que rompe el layout en móvil; WCAG 2.1 AA no medido.
- **Logout** no limpia el contexto de usuario.

### 4.3 Calidad y rendimiento sin medir

- **SUS ≥ 70**, **WCAG 2.1 AA ≥ 95 %**, **LCP < 2.5 s**, **P95 < 10 s**, **TTFT** — todos definidos como umbrales pero sin medición publicada aún.
- **MTTR de failover** del LLM y **grounding/precisión del RAG** — documentados como métricas objetivo, no instrumentados.

### 4.4 Deuda de gestión (parcialmente corregida este ciclo)

- **Naming inconsistente** (`CapacityAR`/`capacityAr`/`Capacitaya`/`Capacita Ya`) — normalizado a **Capacityar**.
- **58 issues fuera del project board** (el tablero tiene 69 items vs 127 issues en la org).
- **Duplicados**: tres issues "S1.5 versionado" (backend#23, backend#55, frontend#20).
- **Branches muertos** sin mergear en `backend` y `frontend`.
- Docs que describían la arquitectura vieja (frontend dentro de Render) — corregidos.

---

## 5. Métricas y porcentajes

### 5.1 Issues

| Repo | Abiertos | Cerrados | Total | % cerrado |
|------|----------|----------|-------|-----------|
| backend | 22 | 34 | 56 | 60.7 % |
| frontend | 17 | 3 | 20 | 15 % |
| .github | 11 | 40 | 51 | 78.4 % |
| **Total** | **50** | **77** | **127** | **60.6 %** |

### 5.2 Commits y cobertura

| Métrica | Valor |
|---------|-------|
| Commits totales | 134 (backend 100 · frontend 9 · .github 25) |
| Cobertura backend | **73 %** (CI ≥ 70 %) |
| Cobertura frontend | **0 %** |

### 5.3 Project board (tras la corrección de estados)

| Estado | Items | % |
|--------|-------|---|
| Done | 28 | 40.6 % |
| In Progress | 1 | 1.4 % |
| Review | 2 | 2.9 % |
| Todo | 8 | 11.6 % |
| Backlog | 17 | 24.6 % |
| Posteriores | 13 | 18.8 % |
| **Total** | **69** | — |

> El tablero cubre 69 de los 127 issues: **58 issues no están representados**.

---

## 6. Riesgos

| Riesgo | Mitigación actual | Pendiente |
|--------|-------------------|-----------|
| Timeout de Render free tier (30 s) | Gap analysis en 2 pasos; timeout + fallback a Mock | Background task con 202 Accepted |
| Cuotas limitadas de LLM gratuitas (Groq/Gemini) | Fallback a Mock + cache por (skill, fase) | Cache de planes por (skill, role) |
| Frontend sin tests ni CI | — (gap abierto) | CI mínimo + tests para el frontend |
| Cobertura backend = 73 %, umbral = 70 % | Piso alcanzado con poco margen | Subir cobertura y sumar branch coverage |
| Datos mockeados en frontend | Marcados como demo en la UI | Conexión real a la API (S4.x) |
| 58 issues fuera del tablero | — (gap de gestión) | Subir issues faltantes al board |
| URL de producción de Vercel sin confirmar | `homepage` del repo limpiado | Verificar la URL exacta del deploy |

---

## 7. Próximos pasos (orden sugerido)

1. **Cerrar el gap de frontend**: tests + CI mínimo, y conectar pantallas a la API real (S4.1–S4.4).
2. **Épica 1** — Perseverancia (`sessions` + `student_metrics`) para cerrar las 5 P.
3. **Épica 2** — Tutores y asignaciones (HITL real).
4. **Épica 3** — Alertas predictivas.
5. **Medir lo definido**: SUS, WCAG, LCP, P95 (los umbrales ya están en `requisitos-calidad.md`).
6. **Sincronizar el tablero**: subir los 58 issues ausentes y consolidar duplicados.

---

## 8. Referencias

| Repo / recurso | Link |
|----------------|------|
| Backend | [Capacityar/backend](https://github.com/Capacityar/backend) |
| Frontend | [Capacityar/frontend](https://github.com/Capacityar/frontend) |
| Organización | [Capacityar/.github](https://github.com/Capacityar/.github) |
| Tablero | [Capacityar Tablero](https://github.com/orgs/Capacityar/projects/1) |
| API (producción) | [capacity-ar-ap.onrender.com](https://capacity-ar-ap.onrender.com) |
| Swagger | [capacity-ar-ap.onrender.com/docs](https://capacity-ar-ap.onrender.com/docs) |
| Sitio (GitHub Pages) | [capacityar.github.io/.github](https://capacityar.github.io/.github/) |
