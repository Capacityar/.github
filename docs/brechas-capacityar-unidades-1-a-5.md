# Análisis de brechas de Capacityar — Unidades I a V

**De:** Scrum Master  
**Para:** Owner del Producto y equipos de desarrollo  
**Asunto:** Diagnóstico incremental de brechas entre el estado actual de Capacityar y los conceptos de las cinco unidades de *Ingeniería de Software en la Práctica*  
**Fecha de elaboración:** 2026-10-16  
**Elaborado por:** Guillermo Fernandez

---

## 1. Propósito del documento

Este análisis compara el estado actual del proyecto **Capacityar** contra los conceptos desarrollados en las **cinco unidades** de la materia *Ingeniería de Software II*. El objetivo es identificar, de forma incremental, qué falta para alinear la práctica del proyecto con cada unidad del libro, y consolidar una matriz de brechas que alimente el plan de sprints.

El análisis es acumulativo: cada unidad conserva los hallazgos de las anteriores y agrega los nuevos.

---

## 2. Contexto del proyecto

| Dimensión | Estado actual |
|-----------|---------------|
| Equipo | 13 personas en 3 equipos + Scrum Master |
| Avance | 43 % — 6 de 14 épicas completadas |
| Issues totales | 29 (21 backend, 8 frontend) |
| Backend | Python/FastAPI, 8 módulos, PostgreSQL/Neon, IA híbrida Groq/Gemini/Mock, 83 commits |
| Frontend | React 19 + Tailwind, 15 páginas, datos mockeados, 4 commits |
| Deuda técnica | Sin autenticación, sin tests, sin Alembic, CORS pendiente |
| Riesgos | Timeout de Render (30 s), cuotas limitadas de LLM gratuitas |

---

## 3. Unidad I — El Software y la Ingeniería de Software

### Conceptos clave

- Naturaleza del software: intangible, no se desgasta, desarrollado, costo concentrado en el desarrollo.
- Dominios de aplicación: sistemas de información, web, IA, embebido, entretenimiento.
- Software heredado y Ley de Hyrum.
- Webapps: cliente-servidor, actualizaciones transparentes, dependencia de red, seguridad, rendimiento, compatibilidad, disponibilidad.
- Ingeniería de software vs. programación: tiempo, escala, compromisos, sostenibilidad.
- Proceso del software: definición, desarrollo, validación, evolución.
- Prácticas de ingeniería: control de versiones, revisión de código, pruebas automatizadas, CI/CD, análisis estático.
- Mitos del software.

### Aplicación a Capacityar

Capacityar es un producto de software que combina los dominios de **software web** y **software de inteligencia artificial**. Cumple con los tres componentes del software: instrucciones (backend FastAPI y frontend React), estructuras de datos (PostgreSQL/Neon) y documentación (manual de usuario, stack tecnológico, informe técnico, requerimientos v2.0). Sin embargo, el proyecto aún está en una etapa donde la programación prima sobre la ingeniería: hay funcionalidad operativa pero poca validación automatizada, gestión de deuda técnica reactiva y procesos de evolución no formalizados.

### Estado actual vs. lo esperado por la unidad

| Aspecto | Estado actual | Lo esperado por la unidad | Brecha |
|---------|---------------|---------------------------|--------|
| Componentes del software | Código, datos y documentación existen | Los tres componentes deben estar alineados y versionados | Documentación técnica parcial; falta documentar decisiones de diseño y deuda técnica |
| Dominio | Webapp + IA | Aplicar consideraciones específicas de ambos dominios | No se explora a fondo el impacto de dependencia de red ni la disponibilidad 24/7 |
| Proceso del software | Desarrollo informal con issues y commits | Cuatro actividades (definición, desarrollo, validación, evolución) de forma explícita | La validación es manual; la evolución carece de lineamientos |
| Prácticas de ingeniería | Git, render desplegado, pocos PRs revisados | Control de versiones, revisión de código, pruebas automatizadas, CI/CD | Sin tests, sin CI, sin revisión sistemática de PRs |
| Mitos | El equipo asume que "funciona = terminado" | Reconocer que el software requiere mantenimiento, documentación y proceso | Se priorizan funcionalidades sobre sostenibilidad |

### Brechas identificadas — Unidad I

1. **B1.1 — Cultura de ingeniería inmadura:** el equipo confunde programación con ingeniería de software; se entrega código sin proceso de validación ni documentación de decisiones.
2. **B1.2 — Validación ausente:** no existen pruebas automatizadas que sostengan las etapas de validación del proceso.
3. **B1.3 — Deuda técnica no gestionada:** faltan autenticación, migraciones de base de datos, CORS y políticas de evolución.
4. **B1.4 — Riesgos de webapp no tratados:** latencia, disponibilidad y seguridad de la aplicación web no están cuantificados ni gestionados.

---

## 4. Unidad II — Gestión de Configuración de Software

### Conceptos clave

- Gestión de configuración de software (GCS): identificación, control de cambios, contabilidad del estado, auditorías.
- Elementos de configuración (EC): código, documentación, datos, infraestructura, entorno.
- Líneas base: requisitos, diseño, código, producto.
- Control de versiones: Git, versionado semántico.
- Control de cambios: correctivos, adaptativos, perfectivos, preventivos.
- Auditorías de configuración: funcionales, físicas, de proceso.
- Informes de estado de configuración.

### Aplicación a Capacityar

El proyecto cuenta con tres repositorios (`backend`, `frontend`, `.github`), ramas `main`/`develop`, versionado semántico informal y 29 issues en GitHub Projects. Esto indica que existe una intención de GCS, pero no un proceso maduro: faltan migraciones de base de datos, no hay política escrita de pull requests, la cobertura de tests es insuficiente y las variables de entorno no están completamente documentadas.

### Estado actual vs. lo esperado por la unidad

| Aspecto | Estado actual | Lo esperado por la unidad | Brecha |
|---------|---------------|---------------------------|--------|
| Elementos de configuración | 3 repos, 8 módulos, 15 páginas, documentos | Todos los EC identificados, con ID, responsable y línea base | No existe un registro formal de ECs ni responsables asignados |
| Control de versiones | Git con ramas main/develop, tags esporádicos | Versionado semántico consistente y tags de release | No hay política clara de branching ni tagging |
| Líneas base | `v0.4.0-stable` propuesta en el plan original | Líneas base de requisitos, diseño, código y producto | Solo se contempla línea base de producto; faltan las demás |
| Control de cambios | Issues en GitHub Projects | Clasificación de cambios y aprobación formal | No se clasifican los issues por tipo de cambio ni hay comité de cambios |
| Auditorías | Code review ocasional | Auditorías funcionales, físicas y de proceso periódicas | No hay auditorías formales ni métricas de cumplimiento |
| Informes de estado | Métricas ad-hoc | Informes de cambios, versiones, auditoría y métricas | No hay informe recurrente de estado de configuración |

### Brechas identificadas — Unidad II (acumuladas)

1. **B1.1 a B1.4** — heredadas de la Unidad I.
2. **B2.1 — ECs no formalizados:** no hay inventario de elementos de configuración con identificador, responsable y línea base.
3. **B2.2 — Migraciones de base de datos ausentes:** el esquema PostgreSQL no está versionado con Alembic.
4. **B2.3 — Política de cambios no escrita:** no se clasifican ni aprueban formalmente los cambios.
5. **B2.4 — Auditorías de configuración no realizadas:** falta verificar consistencia entre lo planificado, documentado y construido.
6. **B2.5 — Variables de entorno incompletas:** solo el 67 % documentadas según auditoría interna.

---

## 5. Unidad III — Diseño Centrado en el Usuario

### Conceptos clave

- Diseño centrado en el usuario (DCU): comprensión explícita de usuarios, participación, evaluación e iteración.
- Reglas doradas de Shneiderman: consistencia, control al usuario, reducción de memorización, retroalimentación, manejo de errores, deshacer.
- Análisis de usuario, tarea, contenido de pantalla y ambiente de trabajo.
- Etapas del diseño de interfaces: descubrir, definir, desarrollar, entregar.
- Diseño de webapps: responsive, mobile-first, accesibilidad, performance, PWA.
- Evaluación del diseño: heurística, tests de usabilidad, SUS, A/B, analytics.

### Aplicación a Capacityar

El frontend cuenta con 15 páginas, un formulario de habilidades de 12 campos, navegación mediante Wouter y un enfoque responsive con Tailwind. Se han realizado entrevistas a usuarios, se definieron 3 personas y se aplicó un test SUS con puntuación de 74/100. Sin embargo, los datos siguen mockeados, los mensajes de error son genéricos, el formulario genera carga cognitiva y no se han establecido estados de carga claros para las operaciones de IA.

### Estado actual vs. lo esperado por la unidad

| Aspecto | Estado actual | Lo esperado por la unidad | Brecha |
|---------|---------------|---------------------------|--------|
| Investigación de usuarios | 12 candidatos y 4 reclutadores entrevistados; 3 personas | Comprensión explícita documentada y validada | Las personas no se actualizan ni se vinculan a requisitos |
| Análisis de tareas | Flujo de diagnóstico modelado informalmente | Modelo detallado con tiempos, fricciones y optimizaciones | No hay registro formal del modelado de tareas |
| Reglas doradas | Consistencia visual parcial; barra de progreso presente | Aplicación sistemática de las 8 reglas | Mensajes de error genéricos; falta deshacer acciones y atajos |
| Diseño webapp | React 19 + Tailwind, 15 páginas, responsive | Mobile-first, accesibilidad WCAG AA, performance optimizada | Datos mockeados; LCP por confirmar; accesibilidad no auditada al 100 % |
| Evaluación del diseño | SUS 74/100, tasa de éxito 87 % | Evaluación heurística, tests con usuarios, métricas de uso | No hay evaluación heurística formal ni mapas de calor |
| Ambiente de trabajo | 65 % móvil, 40 % conectividad variable | Diseño adaptado al contexto real de uso | Falta manejo de estados offline y sincronización |

### Brechas identificadas — Unidad III (acumuladas)

1. **B1.1 a B2.5** — heredadas de las Unidades I y II.
2. **B3.1 — Datos mockeados en frontend:** la experiencia real del usuario no puede validarse hasta conectar con la API.
3. **B3.2 — Formulario con sobrecarga cognitiva:** 12 campos en una sola pantalla sin división en pasos claros.
4. **B3.3 — Mensajes de error deficientes:** no orientan al usuario a recuperarse del error.
5. **B3.4 — Estados de carga y retroalimentación insuficientes:** operaciones de IA de 8-30 s sin feedback claro.
6. **B3.5 — Accesibilidad no verificada:** no se dispone de auditoría WCAG 2.1 AA completa.
7. **B3.6 — Personas y escenarios desconectados de requisitos:** no se trazan a casos de uso ni a decisiones de diseño.

---

## 6. Unidad IV — Modelos de Calidad de Software

### Conceptos clave

- Calidad como relación entre producto, interesados y uso.
- Niveles de calidad: interna, externa, en uso.
- Modelos de calidad de producto: Garvin (cinco perspectivas), McCall (factores operacionales y de transición), ISO/IEC 9126 (reemplazada), ISO/IEC 25010:2023 (nueve características).
- Modelos de madurez de proceso: CMM/CMMI.
- Costos de la calidad: prevención, evaluación, fallo; curva de Boehm.
- Seguridad (*security*) y seguridad funcional (*safety*).
- SQA vs. QC; auditoría vs. peritaje.
- Reingeniería, refactoring y métodos formales.

### Aplicación a Capacityar

Capacityar no tiene requisitos de calidad escritos. No se puede auditar contra una norma si no existen umbrales declarados. La cobertura de tests es del 45 %, pero ese dato no prueba fiabilidad: solo indica disciplina de verificación. El proyecto está entre los niveles 1 y 2 de CMM/CMMI: hay intención de proceso pero no políticas escritas ni verificación sistemática. La seguridad no está formalizada: solo el 67 % de las variables de entorno están documentadas, y no hay evidencia de análisis de *safety* para las recomendaciones generadas por IA.

### Estado actual vs. lo esperado por la unidad

| Aspecto | Estado actual | Lo esperado por la unidad | Brecha |
|---------|---------------|---------------------------|--------|
| Requisitos de calidad | No declarados | Umbral por característica de calidad | No existe un documento de requisitos de calidad |
| Modelo de calidad | Ninguno aplicado formalmente | Selección y aplicación de Garvin, McCall o ISO/IEC 25010 | No se mide ni se reporta con un modelo explícito |
| Cobertura de tests | 45 % (dato de proceso) | Indicador de disciplina, no evidencia de calidad | Se confunde cobertura con fiabilidad |
| Madurez de proceso | Entre nivel 1 y 2 de CMMI | Proceso definido, medido y verificado | Falta política de PRs, medición sin línea base, sin SQA formal |
| Costos de calidad | No registrados | Registro de fase de detección de defectos | No se sabe en qué fase aparecen los 29 issues |
| Seguridad (*security*) | Variables de entorno parcialmente documentadas | Confidencialidad, integridad, responsabilidad, autenticidad, resistencia | No hay auditoría de secretos ni política de seguridad |
| Safety | No evaluado | Advertencia de peligro y *fail safe* en recomendaciones de IA | No se documentan límites ni comportamiento ante fallos de IA |
| SQA / QC | Issues cerrados manualmente (QC) | SQA planificado con independencia y proceso | No hay plan de SQA ni independencia en la garantía |

### Brechas identificadas — Unidad IV (acumuladas)

1. **B1.1 a B3.6** — heredadas de las Unidades I, II y III.
2. **B4.1 — Sin requisitos de calidad:** no hay umbrales declarados para medir ni auditar.
3. **B4.2 — Sin modelo de calidad aplicado:** no se usa Garvin, McCall ni ISO/IEC 25010 de forma explícita.
4. **B4.3 — Cobertura confundida con calidad:** el 45 % de cobertura no es evidencia de fiabilidad.
5. **B4.4 — Proceso inmaduro:** entre nivel 1 y 2 de CMMI; falta política de PRs, medición con línea base y verificación.
6. **B4.5 — Sin registro de fase de detección de defectos:** no se puede calcular el costo de la no calidad.
7. **B4.6 — Seguridad no auditada:** variables de entorno y secretos sin inventario ni control.
8. **B4.7 — Safety de IA no tratado:** no se documentan límites de las recomendaciones ni comportamiento *fail safe*.
9. **B4.8 — Sin plan de SQA:** no hay aseguramiento de calidad planificado e independiente.

---

## 7. Unidad V — Ingeniería de Software de Sistemas de Tiempo Real

### Conceptos clave

- Sistema de tiempo real: correctitud depende del resultado y del instante.
- Clasificación: duro, firme, blando, según la consecuencia del incumplimiento.
- Características: determinismo, concurrencia, fiabilidad, seguridad, verificabilidad.
- Patrones de diseño: *observe and react*, *environmental control*, *process pipeline*.
- Análisis de tiempos: plazo, frecuencia, tiempo de ejecución.
- Interfaz hombre-máquina (HMI): retroalimentación, tiempos percibidos, modelo del diseñador vs. modelo del usuario.
- Gestión del diseño: documentación, trazabilidad, estimación.

### Aplicación a Capacityar

Capacityar se lee como un **sistema de tiempo real blando con personas en el lazo**. Sus latencias reales son: carga inicial de 3,2 s, recomendación por IA de 8 s en promedio y corte por *timeout* de 30 s. Estos plazos no están documentados como requisitos, no se miden percentiles P95/P99 ni usuarios concurrentes, y la interfaz no siempre comunica el estado de espera al usuario. El patrón de diseño del backend se asemeja a un *process pipeline* de 8 módulos, pero no hay un análisis de tiempos formal.

### Estado actual vs. lo esperado por la unidad

| Aspecto | Estado actual | Lo esperado por la unidad | Brecha |
|---------|---------------|---------------------------|--------|
| Clasificación temporal | Latencias presentes pero no clasificadas | Clasificación duro/firme/blando por operación | No se declaran plazos ni consecuencias |
| Presupuesto de latencia | 8 s promedio, 30 s timeout | Plazo objetivo por operación con justificación | No existe objetivo de latencia acordado |
| Análisis de tiempos | Ninguno | Plazo, frecuencia y tiempo de ejecución por tarea | No se planifican prioridades ni capacidad |
| Concurrencia | Sin datos de usuarios concurrentes | Medición y estimación de carga | No se conoce la carga real ni la capacidad |
| HMI y retroalimentación | Estados de carga parciales | Retroalimentación clara para esperas > 10 s | Mensajes genéricos y falta de control de cancelación |
| Trazabilidad de plazos | Timeout de 30 s sin documentación de decisión | Documentar qué, por qué y cuándo de cada plazo | No se registra la razón de los umbrales |
| Fiabilidad | 45 % de cobertura | Evidencia de fiabilidad bajo carga | Cobertura insuficiente; no hay pruebas de estrés |

### Brechas identificadas — Unidad V (acumuladas)

1. **B1.1 a B4.8** — heredadas de las Unidades I a IV.
2. **B5.1 — Sin requisitos de plazo:** las operaciones no tienen plazos declarados ni justificados.
3. **B5.2 — Sin presupuesto de latencia:** no existe objetivo de latencia para carga, recomendación ni timeout.
4. **B5.3 — Sin análisis de tiempos:** no se analiza plazo, frecuencia y tiempo de ejecución por tarea.
5. **B5.4 — Sin datos de concurrencia:** no se miden usuarios concurrentes ni percentiles de latencia.
6. **B5.5 — HMI insuficiente para esperas largas:** falta retroalimentación clara y control de cancelación para operaciones > 10 s.
7. **B5.6 — Sin trazabilidad de decisiones temporales:** el timeout de 30 s y otros plazos no están documentados.
8. **B5.7 — Fiabilidad bajo carga no verificada:** no hay pruebas de carga ni estrés.

---

## 8. Matriz resumen de brechas acumuladas

| ID | Brecha | Unidad que la introduce | Severidad | Equipo responsable de cerrarla |
|----|--------|------------------------|-----------|-------------------------------|
| B1.1 | Cultura de ingeniería inmadura | I | Alta | Scrum Master + todos |
| B1.2 | Validación automatizada ausente | I | Crítica | E1 |
| B1.3 | Deuda técnica no gestionada | I | Alta | E1 + E2 |
| B1.4 | Riesgos de webapp no tratados | I | Media | E2 + E3 |
| B2.1 | ECs no formalizados | II | Media | Scrum Master |
| B2.2 | Migraciones de base de datos ausentes | II | Alta | E1 |
| B2.3 | Política de cambios no escrita | II | Alta | Scrum Master |
| B2.4 | Auditorías de configuración no realizadas | II | Media | Scrum Master |
| B2.5 | Variables de entorno incompletas | II | Alta | E2 |
| B3.1 | Datos mockeados en frontend | III | Alta | E2 + E3 |
| B3.2 | Formulario con sobrecarga cognitiva | III | Media | E3 |
| B3.3 | Mensajes de error deficientes | III | Alta | E3 |
| B3.4 | Estados de carga insuficientes | III | Alta | E3 |
| B3.5 | Accesibilidad no verificada | III | Media | E3 |
| B3.6 | Personas desconectadas de requisitos | III | Media | E3 + Scrum Master |
| B4.1 | Sin requisitos de calidad | IV | Crítica | Scrum Master |
| B4.2 | Sin modelo de calidad aplicado | IV | Media | Scrum Master |
| B4.3 | Cobertura confundida con calidad | IV | Media | E1 + Scrum Master |
| B4.4 | Proceso inmaduro (CMMI 1-2) | IV | Alta | Scrum Master + E2 |
| B4.5 | Sin registro de fase de detección de defectos | IV | Media | Scrum Master |
| B4.6 | Seguridad no auditada | IV | Alta | E1 + E2 |
| B4.7 | Safety de IA no tratado | IV | Alta | E1 + E3 |
| B4.8 | Sin plan de SQA | IV | Crítica | Scrum Master |
| B5.1 | Sin requisitos de plazo | V | Alta | Scrum Master |
| B5.2 | Sin presupuesto de latencia | V | Alta | E2 + E1 |
| B5.3 | Sin análisis de tiempos | V | Media | E1 |
| B5.4 | Sin datos de concurrencia | V | Media | E2 |
| B5.5 | HMI insuficiente para esperas largas | V | Alta | E3 |
| B5.6 | Sin trazabilidad de decisiones temporales | V | Media | Scrum Master |
| B5.7 | Fiabilidad bajo carga no verificada | V | Alta | E1 + E2 |

---

## 9. Consolidación por dimensión

| Dimensión | Brechas críticas | Brechas altas | Brechas medias |
|-----------|------------------|---------------|----------------|
| Proceso y gestión | B4.1, B4.8 | B1.1, B4.4 | B2.1, B2.4, B4.2, B4.5, B5.6 |
| Backend y datos | B1.2 | B1.3, B2.2, B4.6, B4.7, B5.2, B5.7 | B5.3 |
| DevOps e integración | — | B2.5, B3.1, B5.2, B5.7 | B1.4, B5.4 |
| UX/Frontend | — | B3.3, B3.4, B5.5 | B3.2, B3.5, B3.6, B4.7 |

---

## 10. Conclusión

El análisis incremental muestra que Capacityar avanzó en funcionalidad pero acumuló deuda en **proceso, calidad y tiempo**. Las cinco unidades revelan que el proyecto necesita, antes de seguir agregando funcionalidades:

1. **Sentar las bases de ingeniería de software** (Unidad I): proceso, validación y gestión de deuda.
2. **Formalizar la gestión de configuración** (Unidad II): elementos de configuración, líneas base, auditorías.
3. **Validar la experiencia de usuario con datos reales** (Unidad III): conectar frontend, mejorar HMI y accesibilidad.
4. **Definir requisitos de calidad y aplicar un modelo explícito** (Unidad IV): dejar de confundir actividad con calidad.
5. **Tratar al sistema como tiempo real blanco** (Unidad V): plazos, presupuestos de latencia, retroalimentación y trazabilidad.

Estas brechas se traducen en el `plan-de-sprints-v2.md`, que prioriza estabilización, configuración, calidad automatizada, diseño centrado en el usuario, modelos de calidad y tiempo real antes de continuar con las épicas funcionales.

---

## 11. Referencias

- Plan de Sprints — Capacityar (`plan-de-sprints.md`).
- Unidad I: El Software y la Ingeniería de Software (`02-unidad1-software-ingenieria.md`).
- Unidad II: Gestión de Configuración de Software (`03-unidad2-gestion-configuracion.md`).
- Unidad III: Diseño Centrado en el Usuario (`04-unidad3-diseno-centrado-usuario.md`).
- Unidad IV: Modelos de Calidad de Software (`Unidad 4 - Modelos de Calidad de Software.md`).
- Unidad V: Ingeniería de Software de Sistemas de Tiempo Real (`Unidad 5 - Ingeniería de Software de Sistemas de Tiempo Real.md`).
