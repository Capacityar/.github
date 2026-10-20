# Plan de Reestructuración — Capacityar (Frontend a Vercel)

**Fecha de elaboración:** 2026-10-20
**Elaborado por:** Scrum Master
**Estado:** Aprobado, pendiente de ejecución

---

## Objetivo

Separar frontend y backend en repos y deploys independientes: **frontend en Vercel**, **backend en Render**, eliminando la duplicación y el acoplamiento.

## Decisiones

1. **Fuente de verdad del frontend** = `Capacityar/backend/frontend/` (app Vite "capacitaya", 17 páginas reales, la que despliega Render hoy).
2. **El monorepo pnpm** (`Capacityar/frontend/artifacts/` con `api-server`, `capacitaya`, `mockup-sandbox`) es un experimento de Replit → **archivable** (no descartable).
3. **Deploy**: frontend Vercel + backend Render, desacoplados.

## Estado actual (problemas detectados)

| Problema | Detalle |
|----------|---------|
| Frontend duplicado | La app Vite existe en `backend/frontend/` y en `frontend/artifacts/capacitaya` |
| Monorepo experimental | `Capacityar/frontend` es un workspace pnpm de Replit (`.replit-artifact`, `build.mjs`, `mockupPreviewPlugin.ts`) |
| Deploy acoplado | `render.yaml` compila el frontend *dentro* del build del backend (`cd frontend/ && npm run build`) |
| Links rotos | READMEs apuntan a `etrigan16/capacityAr` y `etrigan16/capacitaya` |
| CORS pendiente | Sin `CORSMiddleware`, el frontend de Vercel no podrá llamar a la API de Render |

## Arquitectura objetivo

```
Capacityar/backend    → FastAPI puro (app/), Render (solo API)
Capacityar/frontend   → app Vite "capacitaya" en la raíz, Vercel (SPA estática)
Capacityar/.github    → profile, docs, milestones
```

---

## Fase 1 — Consolidar frontend en `Capacityar/frontend` (E3 + E2)

1. Crear rama `restructure/frontend`.
2. **Archivar** (no borrar) el monorepo: mover a `_archive/` los directorios `artifacts/`, `lib/`, `scripts/` y los archivos `pnpm-workspace.yaml`, `package.json` (workspace), `.replit*`, `replit.md`. Crear tag `pre-vercel`.
3. Copiar el contenido de `backend/frontend/` a la raíz del repo `frontend`.
4. Ajustar `package.json`: `"name": "capacitaya"` (sin scope `@workspace/`).
5. Crear `vercel.json`:
   ```json
   { "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }] }
   ```
6. Crear `.env.example` con `VITE_API_URL=https://capacity-ar-ap.onrender.com`.

**DoD:** repo `frontend` con app Vite en raíz, sin monorepo; `npm run build` funciona.

## Fase 2 — Limpiar backend (E2)

1. Crear rama `restructure/backend-clean`.
2. Eliminar `frontend/` del backend.
3. Actualizar `render.yaml`: quitar `nixPackages` (nodejs) y el paso `cd frontend/ && npm run build`; dejar solo `pip install` + `uvicorn`.
4. Revisar `app/main.py`: quitar el `StaticFiles`/ruta que sirve `frontend/dist`.

**DoD:** `render.yaml` no referencia `frontend/`; el backend arranca solo con Python.

## Fase 3 — CORS (E1) — issue S1.2

1. En `app/main.py`, agregar `CORSMiddleware` permitiendo el origen de Vercel (`https://capacity-ar.vercel.app`) y `http://localhost:5173` (dev).
2. No usar wildcard `*` en producción.

**DoD:** una llamada desde el frontend de Vercel a la API de Render pasa CORS sin error.

## Fase 4 — Deploy en Vercel (E2)

1. Importar `Capacityar/frontend` en Vercel (framework preset **Vite**).
2. Build command `npm run build`, output `dist`.
3. Variable `VITE_API_URL=https://capacity-ar-ap.onrender.com`.
4. Deploy y verificar rutas SPA (sin 404) y llamadas a la API.

**DoD:** app publicada en `*.vercel.app`, navega sin 404 en rutas profundas y llama a la API.

## Fase 5 — Documentación (SM)

1. `backend/README.md`: link frontend → `Capacityar/frontend`, quitar `etrigan16`.
2. `frontend/README.md`: stack real, cómo correr, deploy Vercel, `VITE_API_URL`.
3. `profile/README.md`: enlaces correctos, arquitectura nueva.
4. `plan-de-sprints-v2.md` y issues S0.x actualizados.

**DoD:** ningún README apunta a repos viejos; arquitectura (Vercel/Render) documentada.

---

## Riesgos y mitigaciones

| Riesgo | Mitigación |
|--------|-----------|
| Perder el monorepo | Archivar en `_archive/` + tag `pre-vercel` (no borrar) |
| Bajar el frontend antes de subir Vercel | Fase 4 (Vercel) antes de Fase 2 (quitar `backend/frontend/`) |
| CORS bloquea llamadas | Fase 3 junto con Fase 4, probar end-to-end |

## Orden recomendado (menos downtime)

1. **Fase 1** (consolidar frontend) + **Fase 4** (Vercel arriba) → frontend vivo en Vercel.
2. **Fase 3** (CORS) → habilita Vercel→Render.
3. **Fase 2** (limpiar backend) → recién ahora quitar `backend/frontend/`.
4. **Fase 5** (docs).

## Issues relacionadas

| Issue | Fase |
|-------|------|
| S0.6 [E3] Consolidar frontend | Fase 1 |
| S0.7 [E2] Limpiar backend | Fase 2 |
| S1.2 [E1] CORS (Vercel) | Fase 3 |
| S0.8 [E2] Deploy Vercel | Fase 4 |
| S0.9 [SM] Documentación | Fase 5 |
