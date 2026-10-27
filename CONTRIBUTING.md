# Guía de contribución — Capacityar

**Fecha de elaboración:** 2026-10-27
**Elaborado por:** Scrum Master
**Aplica a:** `Capacityar/backend`, `Capacityar/frontend`, `Capacityar/.github`
**Estado:** Vigente

---

## 1. Para qué sirve este documento

Capacityar no tenía una política escrita de pull requests ni de revisión de código. Eso generó dos problemas concretos:

- El **24/10/2026** los issues `#29` (CORS) y `#30` (Alembic) se resolvieron con commits directos a `main`, sin PR y sin revisión de nadie.
- Cada grupo trabaja con su propio criterio: no hay acuerdo sobre cuándo un cambio está listo, quién lo aprueba ni cómo se mergea.

Esta guía cierra la brecha **B2.3** del análisis de brechas y formaliza lo que el DoD del `plan-de-sprints-v2.md` (§6) ya exigía. Es la **fuente de verdad** del proceso de cambio: si algo no está acá, se propone agregarlo con un pull request.

**No asume que sepas git.** Si nunca usaste ramas ni pull requests, leé la sección 2 y la 3 antes que el resto.

---

## 2. Conceptos desde cero

### 2.1 El modelo mental

Pensá en un documento compartido en la nube:

- La **copia oficial** vive en GitHub. Todos la ven, nadie la edita directo.
- Para cambiar algo, **te llevás una copia** a tu computadora.
- Trabajás en un **borrador paralelo** (la *rama*), sin tocar la oficial.
- Cuando terminás, pedís que **alguien lo revise** antes de pasarlo a la oficial (el *pull request*).
- Si está bien, se **integra** (el *merge*) y tu borrador se descarta.

Eso es todo. Git es la herramienta que hace que ese ida y vuelta no pise el trabajo de los demás.

### 2.2 Glosario mínimo

| Término | Qué es | En una frase |
|---------|--------|--------------|
| **Repositorio** | La carpeta del proyecto, con todo su historial | La carpeta oficial en GitHub |
| **`origin`** | El nombre que git le da al repositorio remoto | "El de GitHub" |
| **Copia local** | Tu clon en tu máquina | Donde trabajás |
| **Commit** | Una foto de tus cambios, con un mensaje | "Guardé esto y expliqué por qué" |
| **Rama** (*branch*) | Una línea de trabajo paralela | Tu borrador |
| **`main`** | La rama oficial del proyecto | La versión que se despliega |
| **Push** | Subir tus commits a GitHub | Publicar tu borrador |
| **Pull request (PR)** | Pedido formal de revisión e integración | "Revisen mi borrador y métanlo" |
| **Review** | Otra persona lee el PR y decide | Aprobar o pedir cambios |
| **Merge** | Integrar la rama a `main` | Pasar el borrador a oficial |
| **Squash** | Aplastar los N commits de la rama en 1 al mergear | Un cambio = un commit en `main` |
| **Tag** | Una marca congelada en un punto del historial | "Versión `v0.5.0`" |
| **CI** | Robot que corre los tests en cada PR | El portero automático |
| **Protección de rama** | Regla de GitHub que impide saltearse el flujo | El candado de `main` |

### 2.3 Los cuatro comandos que vas a usar el 90 % del tiempo

```bash
git switch main && git pull        # pararte en la versión oficial y actualizarla
git switch -c fix/mi-cambio        # crear tu borrador
git add . && git commit -m "..."   # guardar tu trabajo con un mensaje
git push -u origin fix/mi-cambio   # publicar el borrador en GitHub
```

---

## 3. El flujo completo, paso a paso

Ejemplo real: corregir el `CMD` del Dockerfile del backend (fue el issue `backend#20`).

### Paso 1 — Actualizá `main`

```bash
git switch main
git pull
```

Nunca empieces una rama desde una `main` vieja: después aparecen conflictos que no tienen nada que ver con tu cambio.

### Paso 2 — Creá tu rama

```bash
git switch -c fix/s0.1-dockerfile-cmd
```

El nombre sale del issue: `tipo/sX.Y-descripcion-corta`.

### Paso 3 — Hacé el cambio

Editá los archivos que correspondan. Un solo tema por rama.

### Paso 4 — Revisá qué vas a subir

```bash
git status
git diff
```

Si ves archivos que no querías (`.env`, `node_modules`, `__pycache__`), no los agregues.

### Paso 5 — Commiteá

```bash
git add Dockerfile
git commit -m "fix(docker): corregir sintaxis del CMD y normalizar FROM"
```

### Paso 6 — Publicá la rama

```bash
git push -u origin fix/s0.1-dockerfile-cmd
```

### Paso 7 — Abrí el pull request

```bash
gh pr create \
  --title "fix(docker): corregir sintaxis del CMD" \
  --body "Closes #20"
```

O desde la web, con el botón que aparece en el repositorio después del push.

### Paso 8 — Pedí revisión

Agregá el label del equipo que corresponde (`E1`, `E2`, `E3`) y asigná como revisor a **alguien de otro equipo**. Ver sección 7.

### Paso 9 — Atendé los comentarios

Si el revisor pide cambios, commiteás sobre la misma rama y volvés a pushear. El PR se actualiza solo.

### Paso 10 — Merge y limpieza

Cuando está aprobado y el CI en verde, se mergea con **squash**. Después:

```bash
git switch main
git pull
git branch -d fix/s0.1-dockerfile-cmd
```

La rama remota se borra sola al mergear (si `delete_branch_on_merge` está activo, ver Anexo A).

---

## 4. Ramas

### 4.1 El modelo: trunk-based

`main` es la única rama permanente y **siempre debe estar desplegable**. Todo lo demás son ramas cortas que nacen de `main` y vuelven a `main`.

| Regla | Detalle |
|-------|---------|
| Rama permanente | Solo `main` |
| Duración de una rama | Menos de 3 días. Si lleva más, el cambio es demasiado grande |
| Unidad de trabajo | Un issue = una rama = un PR |
| Nacimiento | Siempre desde `main` actualizada |
| Muerte | Se borra al mergear, en el mismo momento |

**Por qué no usamos `main`/`develop`:** la rama `dev` del backend existe pero está **8 commits atrás de `main` y 0 adelante** — nunca se usó. Mantener dos ramas permanentes obliga a promover cambios entre ellas y duplica el trabajo de merge. Con tres grupos y sprints de dos semanas, esa ceremonia no aporta nada. Si en algún momento el proyecto necesita releases paralelas, se revisa esta decisión con un PR a este documento.

### 4.2 Nombres de rama

| Tipo | Prefijo | Ejemplo |
|------|---------|---------|
| Funcionalidad nueva | `feat/` | `feat/s2.1-auth-jwt` |
| Corrección de bug | `fix/` | `fix/s0.1-dockerfile-cmd` |
| Documentación | `docs/` | `docs/s1.4-politica-prs` |
| Tests | `test/` | `test/s2.2-unit-coverage` |
| Refactor sin cambio de comportamiento | `refactor/` | `refactor/gap-engine` |
| Tareas de mantenimiento, dependencias, CI | `chore/` | `chore/update-deps` |
| Urgencia en producción | `hotfix/` | `hotfix/login-500` |

Formato: `prefijo/sX.Y-descripcion-en-minusculas-con-guiones`. Sin mayúsculas, sin espacios, sin acentos, sin nombres de persona.

### 4.3 Prohibiciones

- **No se commitea directo a `main`.** Ni siquiera para un cambio de una línea, ni siquiera para documentación.
- **No se reutiliza una rama** después de mergeada. Rama nueva para el cambio nuevo.
- **No se hace `force-push`** sobre una rama con PR abierto, salvo que sea tu propia rama y avises en el PR. Nunca sobre `main`.

---

## 5. Commits

### 5.1 Formato

```
tipo(alcance): descripción en imperativo
```

- **tipo**: uno de la tabla de 5.2.
- **alcance**: opcional, el módulo o área (`docker`, `cors`, `auth`, `docs`).
- **descripción**: qué hace el commit, en imperativo, minúscula, sin punto final, máximo 72 caracteres.

### 5.2 Tipos

| Tipo | Cuándo |
|------|--------|
| `feat` | Agrega funcionalidad visible para el usuario |
| `fix` | Corrige un comportamiento incorrecto |
| `docs` | Solo documentación |
| `test` | Solo tests |
| `refactor` | Cambia el código sin cambiar su comportamiento |
| `chore` | Dependencias, configuración, tareas de mantenimiento |
| `ci` | Cambios en GitHub Actions o pipelines |

### 5.3 Ejemplos

| Mal | Bien | Por qué |
|-----|------|---------|
| `cambios` | `fix(cors): permitir origen de Vercel en producción` | Dice qué y dónde |
| `arreglado el bug` | `fix(auth): renovar token antes de que expire` | Dice la causa, no el síntoma |
| `update` | `chore(deps): actualizar alembic a 1.14.1` | Identifica el objeto del cambio |
| `WIP` | `feat(gap): calcular brecha por unidad` | Un commit no se sube sin terminar |

### 5.4 Reglas

- **El mensaje explica el por qué**, no el qué. El diff ya muestra qué líneas cambiaron.
- **Un commit = un cambio coherente.** No mezcles un fix con un refactor.
- **Nunca commitees secretos.** Ni `.env`, ni tokens, ni claves. Si pasa, se rota la credencial, no alcanza con borrar el archivo.
- **Referenciá el issue** en el cuerpo cuando el commit no lo cierra: `Refs #36`.

---

## 6. Pull requests

### 6.1 Cuándo abrir uno

Siempre. No hay excepciones por tamaño ni por urgencia (para urgencias, ver sección 10).

### 6.2 Título

Mismo formato que el commit principal: `tipo(alcance): descripción`.

### 6.3 Cuerpo mínimo

```markdown
## Qué
Corrige la sintaxis del CMD en el Dockerfile.

## Por qué
El contenedor no arrancaba en Render por la coma en el array del CMD.

## Cómo verificarlo
1. `docker build -t capacityar .`
2. `docker run -p 8000:8000 capacityar`
3. La API responde en `/health`

Closes #20
```

La línea `Closes #N` es obligatoria: es lo que cierra el issue automáticamente al mergear. Sin ella, el issue queda abierto y el tablero se desincroniza (pasó con `#29` y `#30`).

### 6.4 Tamaño

| Líneas cambiadas | Qué hacer |
|------------------|-----------|
| Menos de 200 | Ideal |
| 200 a 400 | Aceptable, avisá en el PR qué mirar primero |
| Más de 400 | Partilo en PRs encadenados, o justificá en el cuerpo por qué no se puede |

Un PR de 800 líneas no se revisa: se aprueba por cansancio. Eso es peor que no revisar.

### 6.5 Trabajo en curso

Si querés feedback temprano, abrí el PR como **draft**. No se puede mergear hasta que lo marques como listo.

---

## 7. Revisión de código

### 7.1 Quién revisa

| Regla | Detalle |
|-------|---------|
| **Independencia** | Quien escribe el PR **no puede aprobarlo**. Ni el autor, ni alguien que pair-programeó el cambio |
| **Otro rol** | Al menos un aprobador debe tener un rol distinto al del autor: `E1` ↔ `E2` ↔ `E3`, o el **Scrum Master** |
| **Scrum Master** | Puede aprobar PRs de E1, E2 y E3, y cuenta como revisor independiente. **No puede aprobar los PRs que él mismo escribe** |
| **Cantidad** | Un aprobado alcanza. Más revisores son bienvenidos, no obligatorios |

La independencia significa **autor ≠ aprobador**, no "solo ingenieros". Si el autor es de E1, el Scrum Master es un rol distinto y su aprobación es válida.

Ahora, qué certifica cada uno:

| Aprobador | Qué certifica |
|-----------|---------------|
| **Miembro de otro equipo** | Que el cambio funciona y está bien construido (revisión técnica de par) |
| **Scrum Master** | Que el proceso se cumplió: issue vinculado, DoD completo, documentación actualizada, sin secretos en el diff |

El Scrum Master **no reemplaza la revisión técnica**. En PRs que tocan lógica de negocio, seguridad o migraciones, se recomienda además el aprobado del equipo dueño del código — recomendado, no obligatorio, para no volver a crear un cuello de botella.

Esta regla es el DoD §6.2 del plan v2 y la base de la independencia en revisiones que formaliza S5.4. Si el autor y el revisor son la misma persona, la revisión no existe: es una firma.

### 7.2 Qué mira el revisor

Cinco puntos, en este orden:

1. **Funcionalidad** — ¿el cambio hace lo que el issue pide? ¿Hay casos borde sin cubrir?
2. **Tests** — ¿hay tests nuevos o actualizados? ¿Pasan en CI?
3. **Documentación** — ¿el README, la guía o el `.env.example` quedaron actualizados?
4. **Deuda técnica** — ¿introduce un atajo sin registrar? Si lo introduce, ¿está anotado en un issue?
5. **Seguridad y secretos** — ¿hay credenciales, datos personales o endpoints sin autorización?

### 7.3 Cómo responder

| Acción | Cuándo |
|--------|--------|
| **Comment** | Observación que no bloquea |
| **Approve** | Está listo para mergear |
| **Request changes** | Hay algo que debe corregirse antes del merge |

- **Primera respuesta en 24 horas.** Si no podés revisar, decilo en el PR para que el autor busque otro revisor.
- **Quien abre la conversación la cierra.** El autor responde y marca el comentario como resuelto; el revisor no resuelve por él.
- **Crítica al código, no a la persona.** "Este `try/except` se come el error" en vez de "no sabés manejar errores".

### 7.4 Qué no es una revisión

- Aprobar sin leer, para destrabar.
- Reescribir el PR del otro en los comentarios. Si el cambio es grande, se devuelve con una explicación y se vuelve a intentar.
- Pedir cambios de estilo que el proyecto no exige en ningún lado.

### 7.5 Desbloqueo de PRs atascados

El riesgo real no es que alguien apruebe de más: es que un PR quede días esperando respuesta y el trabajo se pierda por silencio. Para eso hay un protocolo con plazos.

| Plazo | Qué pasa |
|-------|----------|
| 0 h | Se abre el PR y se asigna revisor de otro rol. SLA: 24 h |
| +24 h sin respuesta | El autor comenta y pide reasignación a otro miembro del mismo equipo |
| +48 h sin respuesta | El Scrum Master puede aprobar y mergear, dejando registro explícito en el PR |

El registro del desbloqueo es obligatorio:

> Aprobación de desbloqueo: 48 h sin respuesta del revisor asignado. No reemplaza la revisión técnica de par.

Condiciones:

- Aplica a PRs de E1, E2 y E3. **El Scrum Master no puede desbloquear sus propios PRs**: necesita un aprobado de E1, E2 o E3.
- No aplica si el CI está en rojo, hay conflictos o el DoD está incompleto. El silencio se puede saltear; la verificación, no.
- Si el PR toca seguridad, migraciones o lógica de negocio, el desbloqueo exige que el Scrum Master lea el diff completo y lo deje asentado en el PR.

**Alarma de proceso:** si más del 20 % de los PRs de un sprint se mergean por desbloqueo, el problema no es la política sino la capacidad de revisión del equipo. Se trata en la retrospectiva, no bajando el estándar.

---

## 8. Reglas de merge

| Regla | Detalle |
|-------|---------|
| **Método** | **Squash and merge** siempre. `main` queda con un commit por PR |
| **Quién mergea** | El revisor que aprobó, el autor una vez aprobado, o el Scrum Master en un desbloqueo (7.5). Nunca sin aprobación |
| **Rama** | Se borra al mergear |

### 8.1 Qué bloquea un merge

- CI en rojo.
- Revisión pendiente o cambios solicitados sin resolver (salvo el desbloqueo de 7.5).
- Conflictos con `main`.
- El DoD del plan v2 (§6) incompleto: falta documentación, falta test, hay deuda nueva sin registrar.
- El PR no tiene `Closes #N` y no se entiende a qué issue responde.

### 8.2 Qué no se hace

- Mergear el propio PR sin revisión, ni siquiera con el plazo de desbloqueo vencido.
- Mergear con "después lo arreglo". Si no está listo, no se mergea.
- Mergear para liberar la rama.

---

## 9. Versionado semántico

Los releases se identifican con `MAJOR.MINOR.PATCH` (SemVer) y se marcan con un **tag sobre `main`**.

| Número | Cuándo sube | Ejemplo |
|--------|-------------|---------|
| **MAJOR** | Cambio incompatible con lo anterior | `v1.0.0` |
| **MINOR** | Funcionalidad nueva compatible | `v0.5.0` → `v0.6.0` |
| **PATCH** | Corrección compatible | `v0.5.0` → `v0.5.1` |

### 9.1 Por qué seguimos en `0.x.y`

Mientras `MAJOR` sea `0`, el proyecto no tiene contrato de estabilidad: la API y el esquema pueden cambiar sin aviso. `v1.0.0` se marca cuando exista una versión productiva con usuarios reales y compatibilidad comprometida.

### 9.2 Reglas

- El tag se crea **sobre `main`**, después del merge. Nunca sobre una rama de trabajo.
- El tag lo crea el responsable de la tarea de línea base (E2), no cada autor de PR.
- Un tag no se mueve ni se reescribe. Si salió mal, se crea uno nuevo con PATCH+1.
- El número de versión se documenta en el `CHANGELOG.md` del repo.

La primera línea base (`v0.5.0`) se crea en **S1.5**, al cierre del Sprint 1.

---

## 10. Excepciones

### 10.1 Hotfix de producción

Si el sitio está caído, se permite rama `hotfix/` y merge acelerado, con estas condiciones:

1. Avisar en el issue y en el PR, aunque sea después.
2. El PR se abre igual, con `Closes #N`.
3. La revisión se hace **después** del merge, dentro de las 24 horas.
4. Si el hotfix tocó lógica de negocio, se agrega el test que faltaba antes de cerrar el issue.

La excepción es para restaurar el servicio, no para evitar la revisión.

### 10.2 Cambios solo de documentación

Mismo flujo, mismo PR, misma revisión. No hay rama directa a `main` para docs: la documentación desactualizada es tan cara como el código roto.

### 10.3 Revertir un merge

Se revierte con un PR que hace `git revert`, no con `force-push`. El historial no se borra.

---

## Anexo A — Hacer cumplir la política

Una política que nadie aplica es una intención. Hoy **ningún repo de la organización tiene protección de rama**: por eso los commits del 24/10 entraron directo a `main`. La configuración recomendada, a aplicar en `backend` y `frontend` por el responsable de E2 con permisos de administración:

| Regla | Valor |
|-------|-------|
| Target | `refs/heads/main` |
| Require pull request | Sí |
| Aprobaciones requeridas | 1 |
| Descartar aprobaciones al pushear | Sí |
| Requerir aprobación del último push | Sí |
| Bloquear force push | Sí |
| Bloquear borrado de la rama | Sí |

El borrado automático de la rama al mergear es una configuración del repositorio, no del ruleset:

```bash
gh api --method PATCH repos/Capacityar/backend -F delete_branch_on_merge=true
```

El ruleset se aplica así:

```bash
gh api --method POST repos/Capacityar/backend/rulesets \
  --input ruleset-main.json
```

Con `ruleset-main.json`:

```json
{
  "name": "proteccion-main",
  "target": "branch",
  "enforcement": "active",
  "conditions": { "ref_name": { "include": ["refs/heads/main"], "exclude": [] } },
  "rules": [
    { "type": "deletion" },
    { "type": "non_fast_forward" },
    {
      "type": "pull_request",
      "parameters": {
        "required_approving_review_count": 1,
        "dismiss_stale_reviews_on_push": true,
        "require_code_owner_review": false,
        "require_last_push_approval": true,
        "required_review_thread_resolution": false
      }
    }
  ]
}
```

Cuando **S2.4** (CI con GitHub Actions) esté listo, se agrega la regla `required_status_checks` para que el pipeline bloquee el merge automáticamente.

---

## Anexo B — Trazabilidad

| Requisito del issue #36 | Sección de esta guía |
|-------------------------|----------------------|
| Estrategia de branching y versionado semántico | 4 y 9 |
| PR obligatorio por cambio, sin push directo a `main` | 4.3 y 6.1 |
| Revisor de otro rol (independencia) | 7.1 y 7.5 |
| Checklist de revisión | 7.2 |
| Convención de commits y vínculo issue↔PR | 5 y 6.3 |
| Reglas de merge y qué bloquea | 8 |
| Alineación con el DoD del plan v2 §6 | 7.1, 7.2, 8.1 y Anexo B |

| DoD §6 del plan v2 | Dónde se cumple |
|--------------------|-----------------|
| 1. Issue asociado y responsable | 4.1 (un issue = una rama) |
| 2. PR revisado por otro rol | 7.1 y 7.5 |
| 3. Tests pasan en CI | 8.1 (bloqueo) |
| 4. Documentación actualizada | 7.2 punto 3 |
| 5. Sin deuda técnica nueva sin registrar | 7.2 punto 4 y 8.1 |
| 6. Cambios de interfaz validados con UX | 7.2 punto 1 |

---

## Anexo C — Checklist para pegar en el PR

```markdown
- [ ] La rama sale de `main` actualizada y tiene nombre `tipo/sX.Y-descripcion`
- [ ] Commits en formato conventional y con el por qué
- [ ] `Closes #N` en el cuerpo
- [ ] Tests agregados o actualizados, y en verde
- [ ] Documentación actualizada (README, `.env.example`, guía)
- [ ] Sin secretos ni credenciales en el diff
- [ ] Deuda técnica nueva registrada como issue
- [ ] Revisor asignado de otro equipo
```
