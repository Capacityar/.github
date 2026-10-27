# Evaluación heurística del frontend — Capacityar (Sprint 3, S3.2)

**Repositorio evaluado:** `Capacityar/frontend` (commit `bce091b`, rama `main`)
**Cierra:** S3.2 — evaluación heurística del frontend. Alimenta S3.4 (formulario de habilidades en pasos) y S3.5 (accesibilidad y responsive).
**Fecha:** 2026-10-27
**Método:** inspección del código fuente de `src/pages/` y `src/components/AppLayout.tsx`, contrastado con el flujo que describe `backend/docs/frontend-guide.md`. No se ejecutó la aplicación ni se usaron capturas: los datos del prototipo son mockeados, por lo que varios hallazgos describen lo que muestra la UI, no lo que hará en producción.

---

## 1. Set de heurísticas elegido

Se usan las **8 reglas doradas de Shneiderman** (consistencia, atajos, retroalimentación informativa, diálogos de cierre, prevención y manejo de errores, deshacer, control interno del usuario y reducción de la carga de memoria a corto plazo).

**Justificación:**

- La Unidad III de `brechas-capacityar-unidades-1-a-5.md` define el marco de evaluación con estas reglas, y las brechas B3.3 y B3.4 ya están formuladas en esos términos. Usar el mismo set hace que el informe se pueda trazar directamente a la materia y a las brechas.
- Las reglas cubren los problemas de comportamiento más relevantes del prototipo: navegación rota, controles sin efecto, errores poco orientativos y ausencia de retroalimentación en operaciones de IA.
- **Limitación conocida:** Shneiderman no cubre accesibilidad (WCAG) ni diseño responsive. Esos hallazgos se marcan como **Complementaria** y se derivan a S3.5, en lugar de forzarlos dentro de una regla que no corresponde.
- Se descartan las 10 heurísticas de Nielsen como set principal para no duplicar categorías. Pueden usarse como referencia cruzada en una segunda iteración.

**Escala de severidad (0–4):** 0 = no es un problema; 1 = cosmético; 2 = menor, genera fricción; 3 = mayor, bloquea o confunde de forma significativa; 4 = catastrófico, impide completar la tarea o induce a error con consecuencias reales.

---

## 2. Pantallas evaluadas

| Pantalla | Archivo | Rol | Requerida |
|---|---|---|---|
| Login | `pages/Login.tsx` | Todos | Sí |
| Dashboard del alumno | `pages/Dashboard.tsx` | Alumno | Sí |
| Plan de capacitación | `pages/PlanCapacitacion.tsx` | Alumno | Sí |
| Módulo IA (ejercicios) | `pages/ModuloIA.tsx` | Alumno | Agregada (es el bucle principal de aprendizaje) |
| Evaluación / checkpoint | `pages/Evaluacion.tsx` | Alumno | Sí |
| Mis habilidades | `pages/MisHabilidades.tsx` | Alumno | Agregada (base de S3.4) |
| Canal con tutor | `pages/CanalTutor.tsx` | Alumno | Agregada |
| Panel tutor | `pages/PanelTutor.tsx` | Tutor | Sí |
| Análisis de brecha / plan | `pages/TutorGapAnalysis.tsx` | Tutor | Agregada (operación de IA, B3.4) |
| Nuevo puesto | `pages/NuevoPuesto.tsx` | Empresa | Agregada (B3.3) |
| Layout global | `components/AppLayout.tsx` | Todos | Transversal |
| 404 | `pages/not-found.tsx` | Todos | Transversal |

**No revisadas en detalle:** `DetalleCandidato.tsx`, `ProgresoyLogros.tsx`, `ListadoPuestos.tsx`, `ListadoPuestosEvaluacion.tsx`, `TutorCompanias.tsx`, `DemoApi.tsx`. Ver sección 6.

---

## 3. Informe de hallazgos

Cada fila indica la pantalla, el problema observado con su evidencia (archivo y elemento), la heurística violada, la severidad y una recomendación accionable. Los hallazgos marcados con **[B3.x]** cubren una brecha de `brechas-capacityar-unidades-1-a-5.md`.

| ID | Pantalla | Problema | Heurística | Severidad | Recomendación |
|---|---|---|---|---|---|
| H01 | Dashboard, PanelTutor, Evaluación, DetalleCandidato (global) | Varios enlaces internos no coinciden con las rutas registradas en `App.tsx` (que usan prefijo `/student`, `/tutor`). Dashboard: `/modulo` y `/canal-tutor` (CTA "Continuar capacitación" y "Enviar mensaje"). Evaluación: `/progreso`. PanelTutor: `/detalle-candidato`. DetalleCandidato: `/panel-tutor`. Todos caen en la página 404. | Consistencia | **4** | Centralizar las rutas en constantes importadas por todas las páginas y reemplazar los enlaces literales. Agregar un test que recorra los enlaces de cada página contra la lista de rutas. |
| H02 | 404 (global) | La página 404 está en inglés y muestra texto de desarrollo: "404 Page Not Found — Did you forget to add the page to the router?". Un alumno no tiene forma de volver al flujo. **[B3.3]** | Ayuda para reconocer y recuperar errores | **3** | Traducir al español, usar lenguaje de usuario ("Esta página no existe") y ofrecer un botón "Volver al inicio" según el rol (`/student`, `/tutor`, `/companies`). |
| H03 | AppLayout (global) | El sidebar tiene ancho fijo `w-[240px]` sin colapso ni menú. **Verificado en navegador a 390 px:** el sidebar ocupa 240 px y el contenido queda con 150 px; el banner de bienvenida y las tarjetas se cortan en el borde derecho y el texto es ilegible. El documento de brechas indica que el 65 % de los usuarios usa móvil. | Complementaria (responsive, S3.5) | **4** | Ocultar el sidebar bajo `md` y agregar un botón de menú con `Sheet` (ya disponible en `components/ui`). |
| H04 | PanelTutor (sidebar tutor) | El ítem activo es "Mis Candidatos", pero el sidebar del tutor define "Candidatos". Ningún ítem queda resaltado. | Consistencia | **2** | Usar un identificador de sección en lugar de comparar por texto visible, o unificar la etiqueta. |
| H05 | AppLayout (global) | "Salir" es un enlace a `/login`; no limpia el usuario del contexto (`UserContext`). En un equipo compartido, la sesión sigue activa al volver a la pantalla de login. | Control del usuario | **2** | Implementar `logout()` que limpie el contexto y las queries de React Query antes de navegar. |
| H06 | Dashboard, CanalTutor, Evaluación, PanelTutor (global) | Nombres hardcodeados que no corresponden al usuario logueado. Login arranca con `mateo.rodriguez`, pero el Dashboard muestra tutora "Ana García", CanalTutor habla con "Lucía" y Evaluación muestra "¡Excelente trabajo, Lucía!". | Consistencia | **3** | Tomar nombre, tutor y progreso desde `currentUser` y la API. Mientras tanto, marcar explícitamente los datos de demostración. |
| H07 | AppLayout (global) | La campana de notificaciones muestra un punto rojo, pero el botón no tiene acción ni hay panel de notificaciones. Sugiere que hay novedades que no se pueden ver. | Retroalimentación informativa | **2** | Mostrar el punto solo con notificaciones reales, o abrir un panel vacío con "No tenés notificaciones". |
| H08 | Evaluación (global) | La pantalla `Evaluacion.tsx` no está enlazada desde ninguna pantalla auditada (sidebar, ModuloIA ni Dashboard). El checkpoint no es alcanzable desde la interfaz. | Consistencia | **3** | Definir el punto de entrada (por ejemplo, al terminar la última unidad de práctica de un módulo) y enlazarlo desde ModuloIA. |
| H09 | Dashboard | Progreso (47 %), "Habilidades en proceso", "Ya cubiertas" y "Actividad reciente" están fijos en el código. No reflejan el plan real ni las respuestas del alumno. **[B3.1]** | Retroalimentación informativa | **3** | Calcular progreso con `findNextExercise` y los attempts de `frontend-guide.md`. Derivar habilidades del mastery devuelto por la API. |
| H10 | Login | El campo "Contraseña" existe pero el código lo ignora (comentario "ignorada por ahora"). El usuario asume que hay autenticación real. | Consistencia | **3** | Ocultar el campo mientras no haya autenticación, o rotularlo como "Modo demostración". Registrar la brecha de autenticación en el backlog. |
| H11 | Login | El selector Estudiante / Tutor / Empresa solo precarga un email. El rol real sale del usuario encontrado. Elegir "Tutor" y escribir un email de alumno entra como alumno, sin aviso. | Prevención de errores | **2** | Quitar el selector o validar que el rol elegido coincida con el usuario encontrado, mostrando un mensaje claro si no coincide. |
| H12 | Login | Errores genéricos sin acción: "Usuario no encontrado." no indica qué revisar (¿el email está mal escrito?). "Error al conectar con el servidor." no ofrece reintentar. **[B3.3]** | Manejo de errores | **3** | Mensajes específicos: "No encontramos una cuenta con ese email. Revisá que esté bien escrito." y "No pudimos conectar. Reintentá en unos segundos." con botón de reintento. |
| H13 | PlanCapacitacion | Todos los módulos que no están seleccionados muestran un **candado**, aunque no hay bloqueo real (el backend indica que el mastery 80 % es solo informativo, según `frontend-guide.md`). El candado comunica un bloqueo que no existe. | Consistencia | **3** | Mostrar el estado real por módulo (no iniciado, en curso, completado) calculado desde los attempts. Usar candado solo si el backend bloquea. |
| H14 | PlanCapacitacion | No hay progreso visible del plan: ni "X de Y ejercicios" ni porcentaje por módulo, aunque la guía del backend lo prevé. | Retroalimentación informativa | **2** | Mostrar barra de progreso global y por módulo con la fórmula de `frontend-guide.md`. |
| H15 | PlanCapacitacion → ModuloIA | "Comenzar módulo" fija la unidad 0 siempre. El alumno que ya completó la práctica de una unidad vuelve a empezar, y el flujo "continuar donde dejé" de la guía no se usa. | Reducción de la carga de memoria | **3** | Usar `findNextExercise` para fijar la unidad de inicio y mostrar "Continuar en la unidad N" en el botón. |
| H16 | PlanCapacitacion | Error de carga sin botón de reintento. El spinner de carga no tiene texto ni `aria-label`. | Manejo de errores | **2** | Agregar "Reintentar" en el estado de error y un texto de carga ("Cargando tu plan…"). |
| H17 | ModuloIA | `handleSubmitAnswer` solo llama al backend cuando la respuesta es correcta; las incorrectas no se persisten. Si el POST falla, solo hay `console.error`: el alumno no se entera y el avance se pierde en silencio. La guía pide registrar todas las respuestas para recibir `ai_feedback`. | Retroalimentación informativa / Manejo de errores | **3** | Enviar todas las respuestas, mostrar el error con reintento y no marcar el ejercicio como completado hasta confirmar el POST. |
| H18 | ModuloIA | Una respuesta incorrecta muestra solo "✗ Respuesta incorrecta". No hay explicación ni `ai_feedback` (que la API ya devuelve), ni se revela la respuesta correcta tras el fallo. El alumno no sabe qué corregir. | Retroalimentación informativa | **3** | Mostrar `ai_feedback` en una tarjeta debajo de la opción elegida, y la respuesta correcta después del segundo intento o según la política pedagógica. |
| H19 | ModuloIA | Cada clic en una opción es una respuesta: no hay selección previa, ni confirmación, ni forma de corregir un clic accidental. Elegir la opción equivocada por error queda registrado como intento. | Prevención de errores / Deshacer | **3** | Seleccionar → botón "Responder" → confirmación. Permitir cambiar la selección hasta confirmar. |
| H20 | ModuloIA | Al responder no hay indicador de guardado. El alumno no sabe si el envío funcionó. **[B3.4 parcial]** | Retroalimentación informativa | **2** | Mostrar estado "Guardando…" en el botón de la opción y confirmación "Guardado" al terminar. |
| H21 | ModuloIA | En la última unidad, "Siguiente" no hace nada (`handleNext` no tiene rama de cierre). No hay mensaje de que el módulo terminó ni salida clara hacia el plan. | Diálogos de cierre | **2** | Mostrar pantalla de cierre del módulo con resumen y botones "Siguiente módulo" o "Volver a mi plan". |
| H22 | ModuloIA / Evaluación | No hay atajos de teclado para responder (A/B/C/D) ni para avanzar. Las opciones son `<button>`, por lo que son alcanzables con Tab, pero sin atajos el flujo de práctica es lento. | Atajos para usuarios frecuentes | **1** | Agregar teclas A–D para seleccionar y Enter para confirmar. |
| H23 | Evaluación | Las preguntas 1 y 2 son `div` estáticos (no interactivos); las opciones de la 3 son `button` sin `onClick` ni estado seleccionado. "Enviar respuestas" está habilitado sin respuestas y siempre muestra "3/4 correctas · APROBADO" y "¡Excelente trabajo, Lucía!", sin importar lo que contestó el alumno. Un resultado falso en un checkpoint de aprendizaje. | Prevención de errores / Retroalimentación informativa | **4** | Hacer las opciones interactivas con estado real, deshabilitar "Enviar" hasta responder todas, y calcular el resultado desde la API. |
| H24 | Evaluación | La pregunta 4 aparece bloqueada sin explicar por qué ni cuándo se desbloquea. El encabezado dice "3 de 4 preguntas respondidas" aunque la 2 viene pre-respondida sin intervención del alumno. | Consistencia / Retroalimentación informativa | **3** | Mostrar motivo y condición del bloqueo ("Se desbloquea al responder la pregunta anterior"). Contar solo respuestas reales. |
| H25 | Evaluación | El botón "Aún no estoy listo/a" no tiene handler: no hace nada al pulsarlo. El control existe, pero no responde. | Control del usuario | **3** | Implementar la acción (guardar progreso y volver al módulo) o quitar el botón hasta que tenga efecto. |
| H26 | Evaluación | Al enviar, la pantalla no muestra qué respondió bien ni cuál era la respuesta correcta de cada pregunta. | Retroalimentación informativa | **3** | Mostrar por pregunta: respuesta elegida, correcta y explicación breve. |
| H27 | Evaluación | Envío sin confirmación ("¿Enviar? No vas a poder cambiar tus respuestas") y el cierre es solo un texto de resultado. | Diálogos de cierre | **2** | Agregar un diálogo de confirmación previo al envío y un cierre con próximo paso claro. |
| H28 | MisHabilidades | El botón X elimina una habilidad de inmediato, sin confirmación ni opción de deshacer. | Deshacer / Prevención de errores | **3** | Confirmación o toast con "Deshacer" de 5 segundos antes de llamar a la API. |
| H29 | MisHabilidades | `updateLevel` hace `remove` y luego `add`. Si el `add` falla, la habilidad se pierde de la cuenta del alumno. El toast solo dice "Error al actualizar nivel". **Pérdida de datos.** | Manejo de errores / Deshacer | **3** | Usar un endpoint de actualización (PATCH) o revertir el estado local si falla. Hasta entonces, el reintento debe re-agregar la habilidad. |
| H30 | MisHabilidades | Enter con un texto libre llama a `skillsApi.create` y crea la habilidad en el catálogo compartido, sin mostrar sugerencias ni confirmar. Un error de tipeo queda como habilidad oficial para todos. | Prevención de errores | **3** | Mostrar sugerencias antes de crear y pedir confirmación ("Crear 'Excl' en el catálogo?"). Normalizar mayúsculas y acentos para evitar duplicados. |
| H31 | MisHabilidades | Los niveles (básico, intermedio, avanzado) se muestran como botones sin explicación de qué implica cada uno. Toda habilidad nueva arranca en `INTERMEDIATE` sin que el alumno lo elija. | Reducción de la carga de memoria | **2** | Texto de ayuda por nivel y elegir el nivel al agregar la habilidad, sin valor por defecto. |
| H32 | MisHabilidades, TutorGapAnalysis | Los toasts son genéricos: "Error al guardar skill", "Error al crear skill", "Error al actualizar nivel". No indican causa ni qué hacer. **[B3.3]** | Manejo de errores | **3** | Mensajes con causa y acción ("No pudimos guardar 'Excel'. Revisá tu conexión y reintentá.") y botón de reintento. |
| H33 | PanelTutor | Los botones de filtro (Todos, En progreso, Requiere atención, Inactivos) y el campo de búsqueda no tienen `onClick` ni `onChange`. Parecen funcionales y no lo son. | Consistencia / Control del usuario | **3** | Implementar el filtrado o deshabilitar los controles con la etiqueta "Próximamente". |
| H34 | PanelTutor | Progreso fijo en 0 % y estado "Sin iniciar" para todas las filas. "Última actividad" muestra la fecha de creación de la cuenta. El tutor no puede detectar quién necesita atención. | Retroalimentación informativa | **3** | Usar progreso y última actividad reales de la API. Hasta entonces, ocultar las columnas o marcar la tabla como datos de prueba. |
| H35 | PanelTutor | Solo el nombre del candidato es enlace, y todos apuntan a `/detalle-candidato` sin identificador. La fila completa tiene hover y `cursor-pointer`, por lo que sugiere que toda la fila es clicable. | Consistencia | **3** | Hacer la fila completa enlace con el id del candidato (`/tutor/candidatos/:id`) y registrar la ruta. |
| H36 | CanalTutor | El input no tiene `onSubmit` ni handler de envío: el botón de enviar y el clip no hacen nada, y el mensaje escrito se pierde. Los recursos compartidos apuntan a `href="#"`. | Retroalimentación informativa / Manejo de errores | **3** | Conectar el envío a la API (o deshabilitar el canal hasta que exista). Los recursos deben apuntar a URLs reales. |
| H37 | CanalTutor, Dashboard | "En línea", "Ana está escribiendo..." y "Nudge de Ana" están fijos. Muestran una presencia que no existe. | Retroalimentación informativa | **2** | Quitar los indicadores de presencia hasta tener datos reales. |
| H38 | TutorGapAnalysis | El botón cambia a "Analizando..." sin indicar que la operación de IA tarda entre 8 y 30 s, sin progreso ni opción de cancelar. El alumno puede creer que la app se colgó. **[B3.4]** | Retroalimentación informativa | **3** | Mostrar "Analizando con IA, puede tardar hasta 30 segundos" con indicador de progreso, y un botón "Cancelar" (la petición puede abortarse con `AbortController`). Anticipar el timeout de Render. |
| H39 | TutorGapAnalysis | Los errores muestran directamente `err.message` del backend (`setError(err instanceof Error ? err.message : …)`). Se pueden mostrar textos técnicos sin orientación al usuario. **[B3.3]** | Manejo de errores | **2** | Mapear los errores a mensajes en español con acción sugerida y dejar el detalle técnico en consola. |
| H40 | NuevoPuesto | La validación solo muestra el toast "Completá todos los campos" sin indicar cuál falta, y los campos no marcan el error. Se verifican 4 campos visibles (título, descripción, provincia, habilidades), no 12. **[B3.3]** | Prevención de errores / Manejo de errores | **3** | Validar en línea, marcar cada campo con error y su motivo ("Falta el título del puesto"). Enfocar el primer campo inválido. |
| H41 | Global (todas las páginas) | Accesibilidad sin auditar: `src/pages` no tiene ningún atributo `aria-*`, los `<label>` de NuevoPuesto no tienen `htmlFor`, y las opciones de login no comunican el estado seleccionado. | Complementaria (WCAG 2.1 AA, S3.5) | **3** | Agregar `aria-pressed`, `htmlFor` y roles en los controles personalizados. Auditar con axe o Lighthouse. Meta: cumplimiento ≥ 95 % (S3.5). |
| H42 | Dashboard, PlanCapacitacion, ModuloIA | Botones anidados dentro de `<Link>` (`<Link><button/></Link>`). Es HTML inválido. **Verificado en navegador:** Playwright no pudo hacer clic en el botón de "Continuar capacitación" porque el `<a>` contenedor interceptaba el puntero. | Complementaria (accesibilidad, S3.5) | **1** | Reemplazar por `<Link>` con clases de botón, o usar `asChild` si el componente lo permite. |
| H43 | Global (todas las páginas) | **Verificado en navegador:** recargar cualquier pantalla interna pierde el usuario en memoria (`UserContext` no persiste). En `/student/plan` aparece "No se pudo cargar el plan de capacitación" con el header en "Usuario · Candidato". En `/student` el saludo queda como "¡Bienvenid@ de nuevo, !" (nombre vacío). No se redirige al login. | Ayuda para reconocer y recuperar errores / Control del usuario | **3** | Persistir la sesión (por ejemplo, en `sessionStorage` o cookie) o redirigir a `/login` cuando no hay usuario, con mensaje "Tu sesión venció, ingresá de nuevo". |

---

## 3.1 Verificación en navegador

Recorrido con Chromium headless (Playwright) sobre `npm run dev` del commit `bce091b`. Las llamadas a `/api/*` se interceptaron con datos de prueba (un alumno, un tutor y un plan de 3 módulos). Capturas en `shots/` (no versionadas).

| Hallazgo | Resultado observado |
|---|---|
| H01 | El botón "Continuar capacitación" navega a `/modulo` y la página muestra "404 Page Not Found". Confirmado. |
| H03 | A 390 px: sidebar de 240 px y contenido de 150 px, con texto cortado. Confirmado (severidad 4). |
| H04 | En el panel tutor, ningún ítem del sidebar aparece resaltado. Confirmado. |
| H09 | Dashboard muestra "47%" fijo, con independencia de los datos del plan. Confirmado. |
| H13 | Con 3 módulos y uno seleccionado, hay 2 íconos de candado en los módulos no seleccionados. Confirmado. |
| H15 | "Comenzar módulo" abre la unidad 0 del módulo. No se pudo verificar la regresión porque el recorrido no tiene attempts previos. Verificado solo por código. |
| H23 | "Enviar respuestas" está habilitado sin respuestas y muestra "3/4 correctas" igual. La opción B de la pregunta 1 aparece seleccionada sin interacción. Confirmado. |
| H33 | Al pulsar "Requiere atención" la tabla sigue mostrando las 3 filas. Confirmado. |
| H34 | Todas las filas muestran "0%" y "SIN INICIAR". Confirmado. |
| H35 | Todas las filas enlazan a `/detalle-candidato`, sin identificador. Confirmado. |
| H43 | Al recargar `/student/plan` aparece el error de carga. Al recargar `/student`, el saludo queda sin nombre. Confirmado. |

## 4. Cobertura de brechas

| Brecha | Descripción | Hallazgos que la cubren |
|---|---|---|
| **B3.3** — Mensajes de error deficientes | Errores que no orientan a la recuperación | H02, H12, H32, H39, H40 |
| **B3.4** — Estados de carga para IA de 8–30 s | Sin feedback claro durante operaciones de IA | H38 (principal), H20 (parcial) |
| **B3.2** — Formulario con sobrecarga cognitiva | 12 campos en una pantalla | **Sin evidencia en el código auditado** (ver sección 6). H40 y H31 son lo más cercano. |

---

## 5. Priorización para S3.4 y S3.5

**Prioridad 1 — bloquean el flujo o devuelven información falsa (resolver primero):**

1. **H01** — enlaces rotos en el flujo principal (severidad 4).
2. **H23** — evaluación que califica sin datos reales (severidad 4).
3. **H17 y H18** — persistencia silenciosa de respuestas y ausencia de retroalimentación en ModuloIA.
4. **H38, H12, H32, H39, H40** — estados de carga y mensajes de error de IA y formularios (B3.3 y B3.4).
5. **H36** — el canal con el tutor descarta mensajes sin avisar.
6. **H03** — el sidebar deja el contenido ilegible en móvil (severidad 4 verificada en navegador).
7. **H43** — recargar la página pierde la sesión sin redirigir al login.

**Prioridad 2 — alimentan S3.4 (formulario de habilidades en pasos):**

- **H28, H29, H30, H31** — MisHabilidades: confirmación, pérdida de datos al cambiar nivel, creación no controlada en el catálogo y niveles sin explicación. Es la base natural del formulario por pasos: agregar → nivel → confirmar.
- **H19 y H24** — selección y confirmación en pasos, con estado claro en cada paso.
- **H40** — NuevoPuesto: validación en línea y por campo.

**Prioridad 3 — alimentan S3.5 (accesibilidad y responsive):**

- **H03** — sidebar en móvil (65 % de uso móvil).
- **H41** — atributos ARIA, `htmlFor` y estados de controles.
- **H42** — botones anidados en enlaces.
- **H22** — atajos de teclado en la práctica.

**Prioridad 4 — consistencia y datos (dependen de la API, B3.1):**

- H04, H05, H06, H07, H08, H09, H10, H11, H13, H14, H15, H16, H20, H21, H25, H26, H27, H33, H34, H35, H37.

---

## 6. Limitaciones del informe

- **Verificación parcial en navegador.** Se levantó el frontend con Vite y se recorrió con Chromium headless (Playwright), con las llamadas a `/api/*` interceptadas con datos de prueba, porque no hay backend corriendo. Los hallazgos marcados como "Verificado en navegador" (H01, H03, H04, H09, H13, H23, H33, H34, H35, H42, H43) están confirmados visualmente o por comportamiento. El resto sigue siendo inspección de código. Las pruebas de usabilidad con usuarios reales siguen siendo necesarias para validar severidades (S3.3).
- **Datos mockeados.** Varios hallazgos (H06, H09, H23, H34, H37) describen lo que muestra el prototipo. Se mantienen porque, sin datos reales, el usuario ve información falsa; se debe revisar su severidad cuando se conecte la API.
- **Páginas no revisadas en detalle:** `DetalleCandidato.tsx`, `ProgresoyLogros.tsx`, `ListadoPuestos.tsx`, `ListadoPuestosEvaluacion.tsx`, `TutorCompanias.tsx` y `DemoApi.tsx`. Se recomienda una segunda pasada.
- **Brecha B3.2 sin evidencia.** El documento de brechas describe un formulario de 12 campos en una sola pantalla. En el código auditado, el formulario más largo es `NuevoPuesto` con 4 campos visibles, y `MisHabilidades` es un formulario de una sola búsqueda. Hay que confirmar con el equipo a qué pantalla se refiere B3.2 antes de definir el alcance de S3.4.

---

## 7. Observaciones fuera del alcance heurístico

Estas observaciones no son de usabilidad, pero afectan la calidad del producto. Se derivan a E1 (backend) y E2 (DevOps/documentación):

- **`expected_answer` viaja al cliente.** `ModuloIA.tsx` compara la respuesta en el frontend contra `exercise.expected_answer`. Cualquiera puede leer las respuestas correctas en la red o en el código. Además, hay dos fuentes de verdad: el cliente calcula si la respuesta es correcta, y el backend calcula `is_correct`. Derivar a E1 para que el backend sea la única fuente.
- **Autenticación simulada.** El login busca el email en la lista de usuarios y no valida contraseña (ver H10). Cualquier persona que conozca un email puede entrar como ese usuario.
- **Alerta de inactividad comentada.** En `PanelTutor.tsx` el banner de candidatos inactivos está comentado. Si se reactiva, debe usar datos reales (ver H34).
- **Nombre del paquete.** `package.json` tiene `"name": "capacitaya"`, mientras que el proyecto se llama `Capacityar`. Es un detalle menor de consistencia de nombres.

---

## 8. Próximos pasos

1. Corregir H01 y H23 antes de la demo del sprint (rutas y evaluación).
2. Confirmar con el equipo la pantalla de la brecha B3.2 y actualizar `brechas-capacityar-unidades-1-a-5.md`.
3. Usar este informe como entrada del test de usabilidad (S3.3) para validar las severidades 3 y 4 con usuarios reales.
4. Reabrir este informe cuando se conecte la API, para revisar los hallazgos que dependen de datos mockeados.
