# Propuesta de sincronía y asignación de issues — Capacityar

**De:** Scrum Master  
**Para:** Owner del Producto y equipos de desarrollo  
**Asunto:** Auditoría de sincronía entre milestones, project board e issues; propuesta de asignación por equipos funcionales  
**Fecha de elaboración:** 2026-10-17  
**Elaborado por:** Guillermo Fernandez (etrigan16)

---

## 1. Resumen ejecutivo

Este documento resume el resultado de auditar el estado actual de gestión de Capacityar en GitHub y propone cómo alinearlo con el `plan-de-sprints-v2.md`. Se identificaron dos problemas estructurales:

1. **Los milestones están en el repo equivocado.** Los 6 milestones existentes viven en `Capacityar/.github/milestones`, pero los issues reales están en `Capacityar/backend` y `Capacityar/frontend`. Los milestones de un repositorio no se pueden asignar a issues de otro repositorio.
2. **No hay equipos (teams) en GitHub.** Las asignaciones no pueden hacerse a equipos funcionales de forma nativa; hay que usar labels o campos personalizados del project board.

Además, los integrantes de los equipos en GitHub ya no coinciden con los equipos reales del proyecto. El único usuario que se mantiene es el Scrum Master (`etrigan16`). Por eso las asignaciones se harán a **equipos funcionales** (E1, E2, E3, Scrum Master), no a personas de GitHub.

---

## 2. Hallazgos de la auditoría

### 2.1 Milestones

`gh api repos/Capacityar/.github/milestones` devolvió 6 milestones abiertos:

| # | Título | Issues abiertos | Issues cerrados | Descripción |
|---|--------|-----------------|-----------------|-------------|
| 1 | Sprint 0 | 5 | 0 | Estabilización inmediata — línea base `v0.4.0-stable` |
| 2 | Sprint 1 | 5 | 0 | Fundaciones de GCS — línea base `v0.5.0` |
| 3 | Sprint 2 | 5 | 0 | Calidad del software — línea base `v0.6.0` |
| 4 | Sprint 3 | 5 | 0 | Evaluación DCU — línea base `v0.7.0` |
| 5 | Sprint 4 | 6 | 0 | Integración controlada — línea base `v0.8.0` |
| 6 | Sprint 5+ | 9 | 0 | Funcionalidades nuevas sobre base estable (después de `v0.8.0`) |

**Total:** 35 issues abiertos en milestones.

### 2.2 Issues por repositorio

`gh issue list` en `backend` y `frontend` mostró:

| Repo | Issues abiertos | Con milestone asignado | Sin milestone |
|------|-----------------|------------------------|---------------|
| `Capacityar/backend` | 21 | 0 | 21 |
| `Capacityar/frontend` | 8 | 0 | 8 |
| **Total** | **29** | **0** | **29** |

### 2.3 Project board

`gh project item-list --owner Capacityar 1` devolvió 29 items:

| Estado | Cantidad |
|--------|----------|
| Done | 6 |
| In Progress | 1 |
| Backlog | 22 |
| **Total** | **29** |

### 2.4 Discrepancia clave

Los milestones suman **35 issues abiertos**, pero el project board tiene **29 items** y los issues de backend/frontend suman **29 issues abiertos**.

**Explicación:** los milestones de `.github` no están asignados a los issues de `backend` y `frontend`. El contador de 35 en los milestones probablemente refleja una vinculación del project board que no se materializa en el campo `milestone` de cada issue.

### 2.5 Equipos en GitHub

`gh api orgs/Capacityar/teams` devolvió vacío: no hay teams definidos.

Los colaboradores visibles en `backend` y `frontend` son 9 usuarios, pero el documento de planificación indica 13 personas en 3 equipos + Scrum Master. Los 4 faltantes no aparecen como colaboradores de repos o no tienen acceso visible.

---

## 3. Opciones para representar sprints en GitHub

### Opción A — Milestones por repositorio

**Descripción:** crear los mismos milestones (Sprint 0 a Sprint 7+) en `Capacityar/backend` y `Capacityar/frontend`, y asignar cada issue al milestone correspondiente en su propio repo.

**Ventajas:**
- Funciona con el flujo nativo de GitHub.
- Permite filtrar issues por milestone dentro de cada repo.
- Compatible con el concepto de "línea base" del plan de sprints.

**Desventajas:**
- Duplica los milestones en dos repositorios.
- No hay una vista unificada de milestone a nivel de organización (salvo el project board).
- Requiere mantener fechas y descripciones sincronizadas en ambos repos.

**Cuándo elegirla:** si el equipo está acostumbrado a trabajar dentro de cada repo y prefiere no depender del project board para la planificación.

---

### Opción B — Iterations del project board de organización

**Descripción:** usar el campo nativo **Iteration** del GitHub Project `CapacityAr Tablero`. Una iteración es un ciclo de trabajo con fecha de inicio, fecha de fin y título (por ejemplo, "Sprint 5 — Modelos de calidad"). Las iteraciones son propias del project board de organización y se pueden asignar a issues de cualquier repositorio de la org.

**Ventajas:**
- Vista unificada de sprints para backend y frontend.
- No hay duplicación de milestones.
- Permite gráficos de velocidad y carga de trabajo del project board.
- Se alinea mejor con un project board de organización que ya existe.

**Desventajas:**
- Requiere reconfigurar el project board.
- Los milestones existentes en `.github` quedarían obsoletos (habría que archivarlos o eliminarlos).
- Si alguien trabaja solo desde el repo, no verá la iteración asignada en la vista de issues del repo.

**Cuándo elegirla:** si el equipo centraliza la planificación en el project board de organización y quiere una vista única de sprints.

---

### Opción C — Híbrida

**Descripción:** usar milestones por repo para la trazabilidad a nivel de issue, y además crear iteraciones en el project board para la planificación visual conjunta.

**Ventajas:**
- Cubre ambos mundos: repo y project board.
- Permite sincronizar el project board con los milestones.

**Desventajas:**
- Mayor costo de mantenimiento.
- Riesgo de desincronización entre milestones e iteraciones.

**Cuándo elegirla:** si se necesita visibilidad tanto en repos individuales como en el project board.

---

## 4. Asignación de issues a equipos funcionales

Dado que no hay teams en GitHub y los usuarios actuales no representan a los equipos reales, se propone usar **labels** para marcar el equipo responsable de cada issue.

### 4.1 Labels propuestos

| Label | Color sugerido | Equipo |
|-------|----------------|--------|
| `E1` | `#1D76DB` | Backend |
| `E2` | `#0E8A16` | DevOps / Integración |
| `E3` | `#FEF2C0` | UX / Frontend |
| `scrum-master` | `#D93F0B` | Scrum Master / Proceso |

Estos labels se crearían en `Capacityar/backend`, `Capacityar/frontend` y, si se decide mantener milestones en `.github`, también allí.

### 4.2 Criterio de asignación

| Tipo de issue | Equipo | Razón |
|---------------|--------|-------|
| Issues con label `backend` que son lógica de negocio, datos, APIs, tests | E1 | Responsabilidad principal del backend |
| Issues con label `frontend` | E3 | Responsabilidad principal del frontend |
| Issues de despliegue, CORS, CI/CD, Docker, render, integración frontend-backend | E2 | Responsabilidad de DevOps/Integración |
| Issues de documentación de proceso, políticas, informes, calidad, plazos | Scrum Master | Responsabilidad de facilitación y proceso |
| Issues de auth | E1 + E3 | Backend implementa JWT; frontend implementa login/routing |
| Issues de conexión frontend a API | E2 + E3 | Integración + frontend |

### 4.3 Asignación de issues existentes

#### `Capacityar/backend`

| # | Título | Labels actuales | Equipo propuesto | Notas |
|---|--------|-----------------|------------------|-------|
| 1 | C1 - MVP backend FastAPI + módulo users | backend, documentacion, P1, epic-c1 | E1 | Done |
| 2 | C2 - learning_paths con MockPlanGenerator | backend, P1, epic-c2, pasion, play, practica | E1 | Done |
| 3 | C3 - Persistencia PostgreSQL (Neon + Render) | backend, P1, epic-c3 | E1 | Done |
| 4 | C4 - IA: Groq + Gemini con fallback a Mock | enhancement, backend, P1, epic-c4 | E1 | Done |
| 5 | C5 - attempts + Paciencia (4° P) | backend, P1, epic-c5, paciencia | E1 | Done |
| 6 | C6 - resources con anti-alucinación de URLs | enhancement, backend, P2, epic-c6 | E1 | Done |
| 7 | Épica 1 - Perseverancia y métricas del alumno | backend, P1, epic-1, perseverancia | E1 | Backlog |
| 8 | Épica 2 - Tutores y asignaciones (HITL) | backend, hitl, P1, epic-2 | E1 | Backlog |
| 9 | Épica 3 - Sistema de alertas | backend, hitl, P1, epic-3 | E1 | Backlog |
| 10 | Épica 4 - Next-unit logic y desbloqueo progresivo | backend, P1, epic-4, practica | E1 | Backlog |
| 11 | Épica 5 - Validaciones críticas | backend, P2, epic-5 | E1 | Backlog |
| 12 | Épica 6 - Auth (mock + JWT) | backend, auth, P0, epic-6 | E1 | Backlog; frontend colabora en la interfaz |
| 13 | Épica 7 - Eventos hacia Equipo 1 | backend, P2, epic-7 | E1 | Backlog |
| 14 | Épica 8 - Calidad técnica (tests, CORS, Alembic) | backend, calidad, P1, epic-8 | E1 + E2 | Tests/Alembic → E1; CORS → E2 |
| 15 | DOC-1: Informe técnico (backend + frontend) | backend, documentacion, P0, epic-c1 | scrum-master | In Progress |
| 16 | DOC-2: Manual de usuario | backend, documentacion, P1, epic-c1 | scrum-master | Done |
| 17 | DOC-3: Stack tecnológico | backend, documentacion, P1, epic-c1 | scrum-master | Done |
| 18 | DOC-4: Actualizar documento de ingeniería de requerimientos | backend, documentacion, P1, epic-c1 | scrum-master | Backlog |
| 19 | DOC-5: README organización (.github/profile) | backend, documentacion, P1, epic-c1 | scrum-master | Backlog |
| 20 | FIX: Dockerfile CMD sin comas (crítico) | bug, backend, tech-debt, P0 | E2 | Backlog |
| 21 | FIX: render.yaml malformado (indentación) | bug, backend, tech-debt, P0 | E2 | Backlog |

#### `Capacityar/frontend`

| # | Título | Labels actuales | Equipo propuesto | Notas |
|---|--------|-----------------|------------------|-------|
| 1 | FRONT-1: Login + routing real | frontend, auth, P0 | E3 + E1 | Frontend implementa; backend provee auth |
| 2 | FRONT-2: Conectar Dashboard a API real | enhancement, frontend, P1 | E2 + E3 | Integración + frontend |
| 3 | FRONT-3: Conectar Plan de capacitación a API | enhancement, frontend, P1 | E2 + E3 | Integración + frontend |
| 4 | FRONT-4: Conectar Evaluación a API | enhancement, frontend, P1 | E2 + E3 | Integración + frontend |
| 5 | FRONT-5: Conectar Progreso a API | enhancement, frontend, P1 | E2 + E3 | Integración + frontend |
| 6 | FRONT-6: Conectar Canal tutor a API | frontend, hitl, P2 | E2 + E3 | Integración + frontend |
| 7 | FRONT-7: Conectar Panel tutor a API | frontend, hitl, P2 | E2 + E3 | Integración + frontend |
| 8 | FRONT-8: README + documentación del frontend | frontend, documentacion, P2 | E3 | Backlog |

### 4.4 Asignación de issues nuevos propuestos en `plan-de-sprints-v2.md`

Los issues nuevos de la sección 10.7 del plan v2 se asignarían así:

| ID propuesto | Título | Repo | Equipo |
|--------------|--------|------|--------|
| BACK-30 | Implementar Alembic para migraciones versionadas | backend | E1 |
| BACK-31 | Configurar CORS en FastAPI | backend | E2 |
| BACK-32 | Implementar autenticación mock + JWT | backend | E1 |
| BACK-33 | Escribir tests unitarios backend (meta ≥ 70 %) | backend | E1 |
| BACK-34 | Escribir tests de integración de API | backend | E1 |
| BACK-35 | Documentar comportamiento fail safe de recomendaciones de IA | backend | E1 |
| BACK-36 | Implementar timeout y degradación elegante de llamadas a IA | backend | E1 + E2 |
| BACK-37 | Realizar pruebas de carga en endpoints críticos | backend | E1 + E2 |
| BACK-38 | Auditar variables de entorno y secretos | backend | E2 |
| FRONT-9 | Conectar Dashboard a API real | frontend | E2 + E3 |
| FRONT-10 | Conectar Plan de capacitación a API real | frontend | E2 + E3 |
| FRONT-11 | Conectar Evaluación a API real | frontend | E2 + E3 |
| FRONT-12 | Conectar Progreso a API real | frontend | E2 + E3 |
| FRONT-13 | Rediseñar formulario de habilidades en pasos | frontend | E3 |
| FRONT-14 | Mejorar mensajes de error orientados a recuperación | frontend | E3 |
| FRONT-15 | Implementar estados de carga, progreso y cancelación | frontend | E3 |
| FRONT-16 | Alcanzar cumplimiento WCAG 2.1 AA ≥ 95 % | frontend | E3 |
| DOC-6 a DOC-14 | Proceso, calidad, plazos, milestones | .github | scrum-master |

---

## 5. Acciones recomendadas

### 5.1 Independientemente de la opción elegida

1. **Crear labels de equipo** en `backend`, `frontend` y `.github`:
   - `E1`, `E2`, `E3`, `scrum-master`.
2. **Etiquetar todos los issues existentes** con su equipo responsable.
3. **Asegurar que todos los issues estén en el project board.** Hoy hay 29 items en el tablero y 29 issues abiertos; se debe verificar que coincidan uno a uno.
4. **Definir due dates** para los sprints, tanto si se usan milestones como iteraciones.

### 5.2 Si se elige Opción A (milestones por repo)

1. Crear en `Capacityar/backend` los milestones: Sprint 0, Sprint 1, Sprint 2, Sprint 3, Sprint 4, Sprint 5, Sprint 6, Sprint 7+.
2. Crear en `Capacityar/frontend` los mismos milestones.
3. Renombrar en `Capacityar/.github` el milestone "Sprint 5+" a "Sprint 7+ — Funcionalidades nuevas sobre base estable (después de v0.10.0)".
4. Asignar cada issue de backend/frontend a su milestone correspondiente.
5. Crear los issues nuevos (DOC-6 a DOC-14) en `.github` y asignarlos al milestone Sprint 7+ de `.github`.

### 5.3 Si se elige Opción B (iterations del project board)

1. En el project board `CapacityAr Tablero`, crear un campo **Iteration** llamado "Sprint".
2. Crear las iteraciones: Sprint 0 a Sprint 7+ con fechas de inicio y fin.
3. Asignar cada item del project board a su iteración.
4. Archivar o eliminar los milestones obsoletos de `.github`.
5. Opcional: mantener un milestone por repo solo para trazabilidad en la vista de issues del repo.

### 5.4 Si se elige Opción C (híbrida)

1. Crear milestones por repo (Opción A).
2. Crear iteraciones en el project board (Opción B).
3. Mantener sincronizados ambos: cuando un issue cambia de sprint, actualizar milestone e iteración.

---

## 6. Recomendación del Scrum Master

Se recomienda la **Opción B (iterations del project board)** como principal, porque:

- El project board de organización ya existe y es el punto central de planificación.
- Permite una vista unificada de backend y frontend.
- Evita duplicar milestones y mantenerlos sincronizados.
- Se alinea con la idea de "sprints" del plan de sprints.

Como respaldo para quienes trabajan desde la vista de issues del repo, se sugiere mantener labels `E1`, `E2`, `E3` y `scrum-master` para saber quién lidera cada tarea.

Si el equipo prefiere no depender del project board, la **Opción A** es válida y más simple de implementar.

---

## 7. Próximos pasos

1. Elegir Opción A, B o C.
2. Revisar y aprobar la asignación de issues a equipos.
3. Ejecutar la creación de labels, milestones/iteraciones y asignaciones.
4. Crear los issues nuevos de la sección 10.7 del `plan-de-sprints-v2.md`.
5. Actualizar el feature document `capacityar-brechas-unidades-4-5.md` con el resultado de la ejecución.

---

## Anexo — Datos brutos de referencia

### Milestones en `Capacityar/.github/milestones` (2026-10-17)

```json
[
  {"number": 1, "title": "Sprint 0", "open_issues": 5, "closed_issues": 0},
  {"number": 2, "title": "Sprint 1", "open_issues": 5, "closed_issues": 0},
  {"number": 3, "title": "Sprint 2", "open_issues": 5, "closed_issues": 0},
  {"number": 4, "title": "Sprint 3", "open_issues": 5, "closed_issues": 0},
  {"number": 5, "title": "Sprint 4", "open_issues": 6, "closed_issues": 0},
  {"number": 6, "title": "Sprint 5+", "open_issues": 9, "closed_issues": 0}
]
```

### Issues abiertos por repo (2026-10-17)

- `Capacityar/backend`: 21 issues, todos con `milestone: null`.
- `Capacityar/frontend`: 8 issues, todos con `milestone: null`.

### Project board (2026-10-17)

- 29 items: 6 Done, 1 In Progress, 22 Backlog.

### Colaboradores visibles en repos

- `baldosa`, `AgusCabral`, `etrigan16`, `marcelocassiovieira`, `FGiribone`, `JuanFarneda`, `PerrottaJulian`, `Leanpas98` (admin), `LeonardoCravero` (read).

**Nota:** estos usuarios no se asignarán issues directamente. Las asignaciones se harán a equipos funcionales mediante labels.
