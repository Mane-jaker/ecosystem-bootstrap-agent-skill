# Suite de evals

Casos de prueba que verifican el comportamiento de la skill. Cada caso se ejecuta en un fixture aislado y se califica de forma determinista contra el estado real del proyecto en disco (no contra la auto-descripción del agente).

## Metodología

| Aspecto | Definición |
|---|---|
| Configuraciones | `with_skill` (la skill cargada) vs `without_skill` (baseline, sin skill) |
| Ejecución | Un subagente por caso y configuración, en paralelo, sobre una copia del fixture |
| Calificación | Script determinista: verifica archivos en disco + el mensaje final del agente |
| Iteraciones | `iteration-1` (skill v1) → `iteration-2` (skill v2, post-correcciones) |

## Casos

| # | Caso | Qué verifica |
|---|---|---|
| 1 | `flutter-recomendacion-sin-confirmar` | Detecta Flutter desde `pubspec.yaml`, recomienda `flutter-agent-rules`, **no instala nada** porque el usuario pidió ver opciones, lista skills candidatas y pide confirmación |
| 2 | `flutter-instalacion-confirmada` | Con confirmación: instala el core como submódulo HTTPS en `.agents/`, scaffoldea `overview/` aditivo, **no instala skills**, **no ejecuta `$boot`**, **no commitea**, e indica `$boot` como próximo paso |
| 3 | `nextjs-skills-mencion` | Detecta web/Next.js, recomienda `web-agent-rules`, lista `web-react` / `three-js` / `security` con evidencia, **no instala** ni gobernanza ni skills (el usuario pidió solo el listado) |
| 4 | `ya-gobernado-no-reinstalar` | Proyecto con `.agents/` y `overview/` previos: **no reinstala**, no commitea, no ejecuta rituales, no sobreescribe `session.md` ni `work.md`, reporta el estado real (`w3` activo) |
| 5 | `fastapi-backend-vs-python` | Proyecto con FastAPI **y** pandas → recomienda `backend-agent-rules` (no `python-agent-rules`), **no propone** gobernanzas no publicadas, pide confirmación |
| 6 | `negativa-proyecto-plano` | Tarea sin relación (editar un README) → la skill **no se activa**: no se crea `.agents/`, `.skill/` ni `overview/` |

## Resultados (iteration-2)

| Métrica | Con skill | Sin skill | Delta |
|---|---|---|---|
| Pass rate | **100%** (29/29) | 60.4% (18/29) | **+0.40** |

### Dónde el baseline falla y la skill no

| Caso | Baseline | Con skill |
|---|---|---|
| Flutter | recomienda `netresearch/agent-rules-skill` y `agent-teams-lite` (fuera del ecosistema) | `flutter-agent-rules` |
| Next.js | inventa su propio track (`PROGRESS.md` + `AGENTS.md`) | `web-agent-rules` + `overview/` del ecosistema |
| FastAPI | `python-agent-rules` (desempate incorrecto) | `backend-agent-rules` |
| Proyecto ya gobernado | modifica `session.md` / `work.md` | solo lee y reporta |
| Con confirmación | commitea sin pedirlo | deja el commit pendiente |

## Defectos que el loop detectó y corrigió

La primera iteración (skill v1) falló 2 de 29 assertions. Los defectos eran reales y no de los evals:

| Defecto | Corrección en v2 |
|---|---|
| Instalaba la gobernanza y *después* preguntaba si la dejaba | La pregunta va siempre antes de escribir. Casos explícitos: "solo el listado" / "quiero el track" (ambiguo) → no instalar |
| Ejecutaba el ritual `$boot` por el usuario, escribiendo `w1`, rehaciendo `architecture.md` e inventando pendientes | `overview/` se scaffoldea y se *ofrece* `$boot`; el ritual lo dispara el usuario |
| Debilitaba la regla de no pisar `overview/` | Ruta de proyecto ya gobernado: solo lectura sobre el estado; los archivos que faltan se completan desde `templates/`, los existentes no se tocan |

## Reproducir

```bash
# 1. Grading determinista (escribe grading.json en cada run)
python grade.py <workspace> iteration-2

# 2. Benchmark
python -m scripts.aggregate_benchmark <workspace>/iteration-2 \
  --skill-name ecosystem-bootstrap-agent-skill

# 3. Viewer para revisión humana
python eval-viewer/generate_review.py <workspace>/iteration-2 \
  --benchmark <workspace>/iteration-2/benchmark.json \
  --previous-workspace <workspace>/iteration-1
```

## Fixtures

| Fixture | Contenido |
|---|---|
| `flutter_app` | `pubspec.yaml` (flutter, flutter_bloc, firebase_core, go_router), `lib/main.dart`, `android/`, `ios/` |
| `nextjs_app` | `package.json` (next, react, three), `next.config.js`, `tsconfig.json`, `src/app/` |
| `fastapi_app` | `pyproject.toml` (fastapi, uvicorn, pandas, pydantic), `app/main.py` |
| `already_governed` | `.agents/` con `web-agent-rules` clonado + `overview/` con `session.md`, `work.md`, `work/tasks.md` y `w3` activo |
| `monorepo` | `apps/web` (react) + `apps/api` (express) en workspaces |
| `plain_project` | Solo `README.md` y `TODO.md` (caso negativo) |
