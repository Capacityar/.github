# Plan de Sprints v2 — Capacityar

**De:** Owner del Producto  
**Para:** Scrum Master  
**Asunto:** Planificación de sprints de mejora de calidad, proceso, calidad de software y tiempo real para Capacityar  
**Fecha de elaboración:** 2026-10-16  
**Elaborado en conjunto por:** Owner del Producto y Scrum Master

---

## 1. Solicitud del Owner

> El Owner solicita al Scrum Master que, a partir del estado actual del proyecto Capacityar y en base a los conocimientos adquiridos en las **Unidades I, II, III, IV y V** de Ingeniería de Software, se elabore un plan de sprints que priorice la **calidad, el proceso, los modelos de calidad de software y el comportamiento temporal** sobre el agregado de nuevas funcionalidades.
>
> El objetivo es sentar las bases de una ingeniería de software sostenible: control de cambios, gestión de configuración, validación automatizada, diseño centrado en el usuario, requisitos de calidad explícitos y plazos de respuesta gestionados. Sobre esa base se podrán construir las funcionalidades futuras de forma predecible, medible y controlada.

---

## 2. Contexto y motivación

El proyecto **Capacityar** es una plataforma web basada en inteligencia artificial que diagnostica brechas de habilidades entre perfiles profesionales y las demandas del mercado laboral. Al momento de esta planificación, el estado del proyecto es el siguiente:

| Dimensión | Estado |
|-----------|--------|
| Equipo | 13 personas distribuidas en 3 equipos + Scrum Master |
| Avance | 43 % — 6 de 14 épicas completadas |
| Tiempo invertido | 27 horas |
| Tiempo pendiente estimado | 30-42 horas |
| Issues totales | 29 (21 en backend, 8 en frontend) |
| Backend | 83 commits, 8 módulos FastAPI, API REST, PostgreSQL/Neon, IA híbrida Groq/Gemini/Mock |
| Frontend | 4 commits, 15 páginas React 19 + Tailwind, datos aún mockeados |
| Documentación | Manual de usuario, stack tecnológico, informe técnico en progreso, requerimientos v2.0 |
| Deuda técnica identificada | Sin autenticación, sin tests, sin Alembic, CORS pendiente |
| Riesgos | Timeout de Render (30 s), cuotas limitadas de LLM gratuitas |

La decisión de priorizar **calidad, proceso y tiempo** se fundamenta en cinco observaciones:

1. El frontend funciona con datos mockeados, lo que dificulta validar la experiencia real del usuario.
2. No existe validación automatizada, lo que aumenta el riesgo de regresiones al integrar cambios.
3. La deuda técnica no está siendo pagada de forma sistemática, lo que compromete la mantenibilidad futura.
4. No hay requisitos de calidad ni un modelo de calidad aplicado, por lo que no se puede auditar objetivamente.
5. Las latencias del sistema (3,2 s de carga, 8 s de recomendación, 30 s de *timeout*) no están documentadas como plazos ni gestionadas como un sistema de tiempo real blando.

---

## 3. Marco teórico de referencia

Este plan se construye a partir de los conceptos desarrollados en las cinco unidades del libro *Ingeniería de Software en la Práctica*.

### Unidad I — El Software y la Ingeniería de Software

| Concepto | Aplicación en el plan |
|----------|----------------------|
| Proceso del software (definición, desarrollo, validación, evolución) | Cada sprint cubre actividades de las cuatro etapas de forma iterativa. |
| Prácticas de ingeniería de software | Revisión de código, control de versiones, pruebas automatizadas e integración continua. |
| Mitos del software | Se evita el mito "el software es solo programación" documentando, probando y gestionando el proceso. |
| Deuda técnica | Se dedican sprints específicos a pagar deuda técnica antes de agregar funcionalidad. |

### Unidad II — Gestión de Configuración de Software

| Concepto | Aplicación en el plan |
|----------|----------------------|
| Elementos de configuración | Se identifican código, documentación, configuraciones de despliegue y variables de entorno. |
| Control de versiones | Uso de Git con ramas `main` y `develop`, versionado semántico y tags de release. |
| Control de cambios | Todos los cambios pasan por issues, pull requests y revisión de código. |
| Líneas base | Se crea una línea base al finalizar cada sprint de estabilización. |
| Auditorías de configuración | Se audita documentación, cobertura de tests y cumplimiento del proceso. |
| Informes de estado de configuración | El Scrum Master reporta métricas al final de cada sprint. |

### Unidad III — Diseño Centrado en el Usuario

| Concepto | Aplicación en el plan |
|----------|----------------------|
| Análisis del usuario | Se definen personas y escenarios antes de rediseñar flujos. |
| Análisis de la tarea | Se modela el flujo de diagnóstico para reducir la carga cognitiva del usuario. |
| Reglas doradas | Se aplican control al usuario, reducción de memorización y consistencia de interfaz. |
| Diseño de interfaces para webapps | Se prioriza responsive design, accesibilidad y performance. |
| Evaluación del diseño | Se realiza evaluación heurística y test SUS antes de liberar cambios de interfaz. |

### Unidad IV — Modelos de Calidad de Software

| Concepto | Aplicación en el plan |
|----------|----------------------|
| Requisitos de calidad | Se define un umbral por característica de calidad relevante. |
| Modelo ISO/IEC 25010:2023 | Se usa como referencia para auditar el producto. |
| Costos de la calidad | Se registra la fase de detección de cada defecto para calcular costos. |
| SQA / QC | Se separa aseguramiento de calidad del control de calidad con proceso planificado. |
| Seguridad y *safety* | Se auditan secretos, variables de entorno y comportamiento *fail safe* de la IA. |
| Madurez de proceso | Se define política de PRs para avanzar de nivel 1 a nivel 2 de CMMI. |

### Unidad V — Ingeniería de Software de Sistemas de Tiempo Real

| Concepto | Aplicación en el plan |
|----------|----------------------|
| Tiempo real blando | Capacityar se clasifica como sistema de tiempo real blando con personas en el lazo. |
| Plazos y presupuestos de latencia | Se definen plazos objetivo para carga, recomendación y *timeout*. |
| Análisis de tiempos | Se documentan plazo, frecuencia y tiempo de ejecución por tarea crítica. |
| HMI | Se diseñan estados de carga, cancelación y mensajes de error orientados a los plazos percibidos. |
| Trazabilidad | Se documentan las decisiones temporales (por qué 30 s de *timeout*). |

---

## 4. Equipos y roles acordados

| Equipo | Responsabilidad principal | Miembros |
|--------|--------------------------|----------|
| **E1 — Backend** | Arquitectura, datos, lógica de negocio, tests backend, seguridad, análisis de tiempos | Equipo 1 |
| **E2 — DevOps/Integración** | CI/CD, despliegue, conexión frontend-backend, configuración, latencia, concurrencia | Equipo 2 |
| **E3 — UX/Frontend** | Interfaz de usuario, investigación de usuarios, evaluación UX, HMI | Equipo 3 |
| **Scrum Master** | Facilitación, métricas, documentación, control de proceso, SQA, requisitos de calidad | Guillermo Fernandez |

---

## 5. Plan de sprints propuesto por el Scrum Master

### Sprint 0 — Estabilización inmediata

**Objetivo:** Dejar el proyecto en un estado desplegable y documentado antes de introducir cambios de arquitectura.

| ID | Tarea | Responsable | Entregable |
|----|-------|-------------|------------|
| S0.1 | Corregir `Dockerfile` (CMD sin comas) | E2 | Imagen Docker funcional |
| S0.2 | Corregir `render.yaml` (indentación) | E2 | Despliegue en Render estable |
| S0.3 | Cerrar DOC-1: Informe técnico | Scrum Master + E1/E2 | Informe técnico actualizado |
| S0.4 | DOC-5: README de organización | Scrum Master | Perfil de GitHub actualizado |
| S0.5 | FRONT-8: README + documentación del frontend | E3 | Documentación del frontend |

**Línea base objetivo:** `v0.4.0-stable`

---

### Sprint 1 — Fundaciones de Gestión de Configuración de Software

**Objetivo:** Establecer el control de configuración como base para todos los cambios futuros.

| ID | Tarea | Responsable | Entregable |
|----|-------|-------------|------------|
| S1.1 | Implementar Alembic para migraciones de base de datos | E1 | Migraciones versionadas |
| S1.2 | Configurar CORS correctamente | E1 | Middleware CORS en FastAPI |
| S1.3 | Documentar variables de entorno y secretos | E2 | `.env.example` completo y actualizado |
| S1.4 | Definir política de pull requests y code review | Scrum Master | Guía de contribución publicada |
| S1.5 | Establecer versionado semántico y primera línea base | E2 | Tag `v0.5.0` en backend y frontend |

**Conceptos del libro:** Unidad II — elementos de configuración, control de cambios, líneas base.

---

### Sprint 2 — Calidad del software

**Objetivo:** Incorporar validación automatizada y seguridad básica.

| ID | Tarea | Responsable | Entregable |
|----|-------|-------------|------------|
| S2.1 | Implementar autenticación mock + JWT | E1 | Auth funcional en backend |
| S2.2 | Escribir tests unitarios backend (meta: 70 % de cobertura) | E1 | Suite de tests ejecutable |
| S2.3 | Escribir tests de integración de la API | E1 | Tests de endpoints críticos |
| S2.4 | Configurar CI con GitHub Actions (lint + tests) | E2 | Pipeline que bloquea merges con errores |
| S2.5 | FRONT-1: Login + routing real | E3 | Pantalla de login funcional conectada a auth |

**Conceptos del libro:** Unidad I — prácticas de ingeniería, validación; Unidad II — auditorías automatizadas.

**Línea base objetivo:** `v0.6.0`

---

### Sprint 3 — Evaluación de Diseño Centrado en el Usuario

**Objetivo:** Validar la interfaz actual con usuarios reales y aplicar mejoras basadas en evidencia.

| ID | Tarea | Responsable | Entregable |
|----|-------|-------------|------------|
| S3.1 | Definir personas de usuario y escenarios | E3 | 3 personas documentadas |
| S3.2 | Realizar evaluación heurística del frontend | E3 | Informe con problemas y severidad |
| S3.3 | Ejecutar test de usabilidad con 5-8 usuarios + SUS | E3 | Puntuación SUS y recomendaciones |
| S3.4 | Rediseñar formulario de habilidades en pasos | E3 | Wireframes y prototipo validado |
| S3.5 | Mejorar accesibilidad y responsive del frontend | E3 | Cumplimiento WCAG 2.1 AA ≥ 95 % |

**Conceptos del libro:** Unidad III — análisis del usuario, análisis de tareas, evaluación del diseño, reglas doradas.

**Línea base objetivo:** `v0.7.0`

---

### Sprint 4 — Integración controlada

**Objetivo:** Conectar el frontend con el backend de forma estable y auditable.

| ID | Tarea | Responsable | Entregable |
|----|-------|-------------|------------|
| S4.1 | Conectar Dashboard a API real | E2 | Dashboard con datos reales |
| S4.2 | Conectar Plan de capacitación a API real | E2 | Plan de capacitación funcional |
| S4.3 | Conectar Evaluación a API real | E2 | Evaluación con datos reales |
| S4.4 | Conectar Progreso a API real | E2 | Progreso del usuario actualizado |
| S4.5 | Auditoría de configuración post-integración | Scrum Master | Informe de auditoría |
| S4.6 | Generar informe de estado de configuración | Scrum Master | Métricas del sprint documentadas |

**Conceptos del libro:** Unidad I — integración y validación; Unidad II — auditorías e informes de estado.

**Línea base objetivo:** `v0.8.0`

---

### Sprint 5 — Modelos de calidad de software y SQA

**Objetivo:** Definir requisitos de calidad, aplicar un modelo de calidad explícito y establecer aseguramiento de calidad.

| ID | Tarea | Responsable | Entregable |
|----|-------|-------------|------------|
| S5.1 | Escribir especificación de requisitos de calidad | Scrum Master | Documento con umbrales por característica |
| S5.2 | Aplicar evaluación contra ISO/IEC 25010:2023 | Scrum Master + E1/E2/E3 | Informe de brechas de calidad |
| S5.3 | Auditar variables de entorno y secretos | E2 | Inventario de secretos y política de rotación |
| S5.4 | Definir plan de SQA e independencia en revisiones | Scrum Master | Plan de aseguramiento de calidad publicado |
| S5.5 | Implementar registro de fase de detección de issues | Scrum Master | Template de issue con campo "fase de detección" |
| S5.6 | Documentar comportamiento *fail safe* de recomendaciones de IA | E1 + E3 | Especificación de límites y advertencias |

**Conceptos del libro:** Unidad IV — ISO/IEC 25010, costos de calidad, SQA, seguridad, *safety*, CMMI.

**Línea base objetivo:** `v0.9.0`

---

### Sprint 6 — Ingeniería de tiempo real e HMI

**Objetivo:** Gestionar los plazos del sistema como un sistema de tiempo real blando con personas en el lazo.

| ID | Tarea | Responsable | Entregable |
|----|-------|-------------|------------|
| S6.1 | Definir requisitos de plazo por operación | Scrum Master + E1/E2 | Documento de presupuestos de latencia |
| S6.2 | Implementar estados de carga, progreso y cancelación en frontend | E3 | Componentes de feedback para operaciones > 10 s |
| S6.3 | Medir percentiles P95/P99 y usuarios concurrentes | E2 | Dashboard o informe de latencia |
| S6.4 | Implementar *timeout* y degradación elegante de llamadas a IA | E1 + E2 | Fallback de IA con estado claro para el usuario |
| S6.5 | Documentar decisiones temporales (*timeout* 30 s, plazos objetivo) | Scrum Master | Registro de decisiones en wiki |
| S6.6 | Realizar pruebas de carga básicas en endpoints críticos | E1 + E2 | Informe de pruebas de carga |

**Conceptos del libro:** Unidad V — tiempo real blando, análisis de tiempos, HMI, trazabilidad.

**Línea base objetivo:** `v0.10.0`

---

### Sprint 7 en adelante — Funcionalidades nuevas sobre base estable

**Objetivo:** Avanzar las épicas funcionales pendientes sobre una base técnica, de proceso, de calidad y temporal consolidada.

| Épica | Tema | Responsable principal |
|-------|------|----------------------|
| Épica 1 | Perseverancia y métricas del alumno | E1 |
| Épica 2 | Tutores y asignaciones (HITL) | E1 + E3 |
| Épica 3 | Sistema de alertas | E2 |
| Épica 4 | Next-unit logic y desbloqueo progresivo | E1 |
| Épica 5 | Validaciones críticas | E1 |
| Épica 6 | Auth (mock + JWT) — *parcialmente cubierto en Sprint 2* | E1 |
| Épica 7 | Eventos hacia Equipo 1 | E2 |
| FRONT-6 | Conectar Canal tutor a API | E3 |
| FRONT-7 | Conectar Panel tutor a API | E3 |

---

## 6. Definición de terminado (DoD) acordada

Una tarea se considera terminada cuando cumple **todas** las siguientes condiciones:

1. Está asociada a un issue del tablero y tiene asignado un responsable.
2. Tiene un pull request revisado y aprobado por al menos un miembro de otro equipo.
3. Los tests automatizados pasan en CI (donde aplique).
4. La documentación técnica o de usuario está actualizada.
5. No aumenta la deuda técnica sin un registro explícito y aprobado.
6. En el caso de cambios de interfaz, se valida con al menos una técnica de evaluación UX.
7. **Nuevo — Calidad:** si el cambio afecta un requisito de calidad, se registra evidencia de cumplimiento del umbral.
8. **Nuevo — Tiempo real:** si el cambio afecta una operación con plazo, se documenta el plazo objetivo y se verifica que no se degrade.

---

## 7. Métricas de seguimiento acordadas

| Métrica | Meta | Responsable de reportar | Frecuencia |
|---------|------|-------------------------|------------|
| Cobertura de tests | ≥ 70 % | E1 | Cada sprint |
| Pull requests revisados | 100 % | Scrum Master | Cada sprint |
| Issues cerrados vs planificados | ≥ 80 % | Scrum Master | Semanal |
| Puntuación SUS | ≥ 70/100 | E3 | Cada evaluación UX |
| Líneas base creadas | 1 por sprint de estabilización | E2 | Cada sprint |
| Documentación actualizada | 100 % de docs del sprint | Scrum Master | Cada sprint |
| Tiempo de carga inicial (LCP) | < 2.5 s | E2 | Cada sprint |
| Deuda técnica documentada | 0 ítems sin registro | Scrum Master | Cada sprint |
| Requisitos de calidad definidos | ≥ 1 por característica relevante | Scrum Master | Sprint 5 |
| Fase de detección registrada en issues | 100 % | Scrum Master | A partir de Sprint 5 |
| Latencia P95 de recomendación | < 10 s | E2 | A partir de Sprint 6 |
| Usuarios concurrentes soportados | Documentado | E2 | Sprint 6 |

---

## 8. Aprobación del Owner

> El Owner revisa y aprueba el plan de sprints v2 presentado por el Scrum Master. Se autoriza el inicio del **Sprint 0** con enfoque en estabilización, calidad, proceso, modelos de calidad y tiempo real, posponiendo la ejecución de las épicas funcionales 1-7 hasta que se alcance la línea base `v0.10.0`.

| Rol | Nombre | Firma | Fecha |
|-----|--------|-------|-------|
| **Owner del Producto** | | | |
| **Scrum Master** | Guillermo Fernandez | | |

---

## 9. Cambios respecto del plan original y por qué

| Aspecto | Plan original (`plan-de-sprints.md`) | Plan v2 | Justificación |
|---------|--------------------------------------|---------|---------------|
| Unidades cubiertas | I, II, III | I, II, III, IV, V | El curso avanzó y el Owner pidió incorporar calidad de software y tiempo real. |
| Sprints de estabilización | 0 a 4 | 0 a 6 | Se agregan Sprint 5 (calidad/SQA) y Sprint 6 (tiempo real/HMI). |
| Línea base funcional objetivo | `v0.8.0` | `v0.10.0` | Se requieren dos líneas base adicionales para cubrir U4 y U5 antes de funcionalidades nuevas. |
| Requisitos de calidad | No contemplados | Sprint 5 dedicado | La Unidad IV señala que sin requisitos de calidad no hay auditoría posible. |
| SQA | No contemplado | Plan de SQA en Sprint 5 | Separar aseguramiento de control de calidad. |
| Seguridad y *safety* | Parcial (auth mock) | Auditoría de secretos y *fail safe* de IA | La Unidad IV distingue *security* y *safety*. |
| Plazos y latencia | Mencionados como riesgo | Sprint 6 dedicado a requisitos de plazo, percentiles, HMI y pruebas de carga | La Unidad V clasifica a Capacityar como tiempo real blando. |
| DoD | 6 condiciones | 8 condiciones | Se agregan verificación de requisitos de calidad y de plazos. |
| Métricas | 8 métricas | 12 métricas | Se agregan requisitos de calidad, fase de detección, latencia P95 y usuarios concurrentes. |

La decisión central fue **no acelerar a funcionalidades nuevas** hasta tener: (1) configuración y tests, (2) diseño centrado en el usuario validado, (3) integración real, (4) requisitos de calidad y SQA, y (5) plazos gestionados como sistema de tiempo real blando.

---

## 10. Evaluación de cambios en repositorios

> Esta sección evalúa los cambios necesarios en los repositorios de Capacityar para alinear el tablero, los *milestones* y los *issues* con el `plan-de-sprints-v2.md`. No se modifican repositorios remotos; solo se proponen acciones.

### 10.1 Milestones actuales (datos reales de GitHub)

Estado de `Capacityar/.github/milestones` al momento de la revisión:

| # | Título | Estado | Issues abiertos | Issues cerrados | Descripción |
|---|--------|--------|-----------------|-----------------|-------------|
| 1 | Sprint 0 | open | 5 | 0 | Estabilización inmediata — línea base `v0.4.0-stable` |
| 2 | Sprint 1 | open | 5 | 0 | Fundaciones de Gestión de Configuración de Software — línea base `v0.5.0` |
| 3 | Sprint 2 | open | 5 | 0 | Calidad del software — línea base `v0.6.0` |
| 4 | Sprint 3 | open | 5 | 0 | Evaluación de Diseño Centrado en el Usuario — línea base `v0.7.0` |
| 5 | Sprint 4 | open | 6 | 0 | Integración controlada — línea base `v0.8.0` |
| 6 | Sprint 5+ | open | 9 | 0 | Funcionalidades nuevas sobre base estable (después de `v0.8.0`) |

**Observación:** los 6 milestones existentes coinciden exactamente con el `plan-de-sprints.md` original. Ninguno tiene fecha de vencimiento y ninguno tiene issues cerrados.

### 10.2 Project board actual (datos reales de GitHub)

Estado de `github.com/orgs/Capacityar/projects/1/views/1` al momento de la revisión:

| Estado | Cantidad | Notas |
|--------|----------|-------|
| Done | 6 | Épicas C1-C6 completadas |
| In Progress | 1 | DOC-1: Informe técnico |
| Backlog | 22 | Épicas 1-7, FRONT-1 a FRONT-8, DOC-2 a DOC-5, FIX-20, FIX-21, Épica 8 |
| **Total** | **29** | |

**Discrepancia detectada:** los milestones suman **35 issues abiertos**, pero el project board muestra **29 items**. Eso indica que hay issues asignados a milestones que no están en el tablero, o viceversa. Antes de reorganizar conviene sincronizar ambas vistas.

### 10.3 Milestones a modificar

| Milestone actual | Cambio propuesto | Razón |
|------------------|------------------|-------|
| Sprint 5+ | **Renombrar** a "Sprint 7+ — Funcionalidades nuevas sobre base estable (después de `v0.10.0`)" | Las funcionalidades nuevas se posponen hasta después de `v0.10.0`. Sus 9 issues se reasignan a `v0.11.0+`. |
| Sprint 0 a Sprint 4 | **Mantener** títulos y versiones | Siguen siendo válidos en el plan v2. |
| Todos los milestones | **Agregar fechas tentativas** alineadas a los sprints | Hoy ninguno tiene due date; sin fechas no hay métrica de cierre. |

### 10.4 Milestones a crear

| Nuevo milestone | Versión | Descripción | Due date tentativa |
|-----------------|---------|-------------|--------------------|
| Sprint 5 — Modelos de calidad de software y SQA | `v0.9.0` | Definir requisitos de calidad, aplicar ISO/IEC 25010:2023, SQA, seguridad y *safety* | Fin de Sprint 5 |
| Sprint 6 — Ingeniería de tiempo real e HMI | `v0.10.0` | Definir plazos, medir latencias, mejorar HMI y realizar pruebas de carga | Fin de Sprint 6 |
| Base estable para funcionalidades | `v0.10.0-stable` | Línea base de producto consolidada antes de épicas funcionales | Fin de Sprint 6 |

### 10.5 Milestones a eliminar (si aplica)

No se propone eliminar ninguno de los 6 milestones existentes. El milestone "Sprint 5+" debe renombrarse y reasignar sus issues, no borrarse, para no perder el historial.

### 10.7 Issues nuevos propuestos

A partir del análisis de brechas (`brechas-capacityar-unidades-1-a-5.md`), se proponen los siguientes issues para ambos repositorios:

#### Backend (`Capacityar/backend`)

| ID propuesto | Título | Responsable | Vinculado a brecha |
|--------------|--------|-------------|-------------------|
| BACK-30 | Implementar Alembic para migraciones versionadas | E1 | B2.2 |
| BACK-31 | Configurar CORS en FastAPI | E1 | B1.3 |
| BACK-32 | Implementar autenticación mock + JWT | E1 | B1.3 |
| BACK-33 | Escribir tests unitarios backend (meta ≥ 70 %) | E1 | B1.2, B4.3 |
| BACK-34 | Escribir tests de integración de API | E1 | B1.2 |
| BACK-35 | Documentar comportamiento *fail safe* de recomendaciones de IA | E1 | B4.7 |
| BACK-36 | Implementar *timeout* y degradación elegante de llamadas a IA | E1 + E2 | B5.4, B5.7 |
| BACK-37 | Realizar pruebas de carga en endpoints críticos | E1 + E2 | B5.7 |
| BACK-38 | Auditar variables de entorno y secretos | E2 | B4.6 |

#### Frontend (`Capacityar/frontend`)

| ID propuesto | Título | Responsable | Vinculado a brecha |
|--------------|--------|-------------|-------------------|
| FRONT-9 | Conectar Dashboard a API real | E2 + E3 | B3.1 |
| FRONT-10 | Conectar Plan de capacitación a API real | E2 + E3 | B3.1 |
| FRONT-11 | Conectar Evaluación a API real | E2 + E3 | B3.1 |
| FRONT-12 | Conectar Progreso a API real | E2 + E3 | B3.1 |
| FRONT-13 | Rediseñar formulario de habilidades en pasos | E3 | B3.2 |
| FRONT-14 | Mejorar mensajes de error orientados a recuperación | E3 | B3.3 |
| FRONT-15 | Implementar estados de carga, progreso y cancelación | E3 | B3.4, B5.5 |
| FRONT-16 | Alcanzar cumplimiento WCAG 2.1 AA ≥ 95 % | E3 | B3.5 |

#### Organización / proceso (`Capacityar/.github`)

| ID propuesto | Título | Responsable | Vinculado a brecha |
|--------------|--------|-------------|-------------------|
| DOC-6 | Definir política de pull requests y code review | Scrum Master | B2.3, B4.4 |
| DOC-7 | Crear inventario de elementos de configuración | Scrum Master | B2.1 |
| DOC-8 | Escribir especificación de requisitos de calidad | Scrum Master | B4.1 |
| DOC-9 | Aplicar evaluación contra ISO/IEC 25010:2023 | Scrum Master | B4.2 |
| DOC-10 | Definir plan de SQA | Scrum Master | B4.8 |
| DOC-11 | Implementar registro de fase de detección en issues | Scrum Master | B4.5 |
| DOC-12 | Definir requisitos de plazo y presupuestos de latencia | Scrum Master + E2 | B5.1, B5.2 |
| DOC-13 | Documentar decisiones temporales (*timeout*, plazos objetivo) | Scrum Master | B5.6 |
| DOC-14 | Crear milestones `v0.9.0` y `v0.10.0` en `.github/milestones` | Scrum Master | — |

### 10.6 Acciones recomendadas en GitHub (basadas en datos reales)

1. **Auditar sincronía milestones ↔ project board**
   - Los 6 milestones suman 35 issues abiertos; el project board tiene 29 items.
   - Identificar los 6 issues que están en milestones pero no en el tablero, y viceversa.
   - Decidir si todos los issues deben estar en el project board.

2. **Crear milestones**
   - `Sprint 5 — Modelos de calidad de software y SQA` (`v0.9.0`).
   - `Sprint 6 — Ingeniería de tiempo real e HMI` (`v0.10.0`).
   - `Base estable para funcionalidades` (`v0.10.0-stable`).
   - Asignar due dates tentativas.

3. **Modificar milestone existente**
   - Renombrar `Sprint 5+` a `Sprint 7+ — Funcionalidades nuevas sobre base estable (después de v0.10.0)`.
   - Reasignar sus 9 issues abiertos al nuevo milestone renombrado (o a `v0.11.0+`).

4. **Reasignar issues existentes a los nuevos sprints**
   - Épica 8 — Calidad técnica (tests, CORS, Alembic) → Sprint 2 (ya existe) o Sprint 5 si se prefiere consolidar calidad.
   - FRONT-1 — Login + routing real → Sprint 2 (autenticación).
   - FRONT-8 — README frontend → Sprint 0 (documentación).
   - FIX-20 y FIX-21 → Sprint 0 (estabilización).
   - DOC-1 — Informe técnico → Sprint 0 o Sprint 1.

5. **Crear issues nuevos** derivados de las brechas de las Unidades IV y V (ver tabla en 10.7).

6. **Actualizar el project board**
   - Agregar campos personalizados: `fase de detección`, `requisito de calidad`, `plazo objetivo (s)`.
   - Crear vistas por sprint o por equipo si no existen.

7. **No eliminar milestones** sin reasignar primero sus issues.

---

## 11. Notas de confección conjunta

Este documento fue elaborado en conjunto entre el Owner del Producto y el Scrum Master. El Owner aportó la visión estratégica y la priorización de calidad, proceso, calidad de software y tiempo real; el Scrum Master aportó la traducción operativa en sprints, equipos, métricas y propuestas de gestión en GitHub. Ambos acuerdan revisar este plan al finalizar cada sprint para adaptarlo a los hallazgos y al avance del proyecto.

El documento queda abierto para incorporar los aprendizajes de la Unidad VI a medida que avance la materia.
