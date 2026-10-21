# Brain — Motor de Decisiones

Matrices de decisión: qué hacer ante cada situación. El objetivo es que la skill decida lo mismo cada vez, y que explique por qué.

---

## 🧭 Matriz 1 — ¿Actúo o no?

| Situación | Decisión | Razón |
|---|---|---|
| Proyecto sin `.agents/`, el usuario dice "empecemos" / "nuevo proyecto" | **Actuar**: evaluar e informar | Es el momento natural para instalar la gobernanza; sin `.agents/` no hay track vivo |
| Proyecto sin `.agents/`, el usuario pide un cambio puntual de código | **No actuar** | No pidió gobernanza. Si el stack es obvio, mencioná en una línea que existe la opción y seguí |
| Proyecto con `.agents/` | **No reinstalar** → `$ecoproject:audit` | La gobernanza vigente manda; duplicarla rompe la trazabilidad |
| El usuario pide explícitamente "instala la gobernanza" / "mete agent rules" | **Actuar**: informe + pregunta | Confirmación explícita del flujo, pero la instalación igual requiere el sí explícito del Paso 3 |
| El usuario pide "solo el listado" / "quiero ver las opciones" / "por ahora no instales" | **Solo informe.** No escribas nada | No pidió instalación. Un "por ahora" es un no |
| El usuario dice "quiero el track vivo de avances" (ambiguo) | **Informe + pregunta** | La gobernanza es lo que habilita el track, pero también lo que escribe en el proyecto. Ante ambigüedad, preguntar |
| El usuario pide una skill concreta (`$bloc`, `$security`) | **Instalar directo** en `.skill/` | No hace falta el catálogo completo ni elegir gobernanza. Y solo esa skill: nada de `.agents/` ni `overview/` |
| El core quedó recién instalado y `overview/` a medias | **Scaffoldear y ofrecer `$boot`** | No corras `$boot` vos: escribiría tareas, deuda y arquitectura que el usuario no pidió |

## 🧭 Matriz 2 — Detección de dominio

| Si el proyecto tiene… | Entonces dominio = | Señal de peso alto |
|---|---|---|
| `pubspec.yaml` con `flutter` en `dependencies` o `environment.sdk: flutter` | `flutter` | el propio `pubspec.yaml` |
| `package.json` con `react`/`next`/`vue`/`svelte`/`astro`/`nuxt`/`@angular/core` | `web` | la dependencia del framework |
| `go.mod` | `go` | el archivo |
| `*.kt` + (`build.gradle.kts` \| `settings.gradle.kts`) | `kotlin` | `build.gradle.kts` |
| `Cargo.toml` | `rust` | el archivo |
| `Package.swift` \| `*.xcodeproj` \| `*.xcworkspace` | `swift` | cualquiera de los tres |
| `pyproject.toml`/`requirements.txt`/`Pipfile` + fastapi/django/flask/starlette/uvicorn | `backend` | la dependencia web |
| `pyproject.toml`/`requirements.txt`/`Pipfile` + pandas/langchain/airflow/dask | `python` | la dependencia de datos/IA |
| `package.json` con express/fastify/@nestjs/koa/hapi | `backend` | la dependencia del server |
| `go.mod` + gin/echo/chi/grpc/fiber | `backend` | la dependencia del server |
| `Cargo.toml` + axum/actix/rocket/warp | `backend` | la dependencia del server |
| `project.godot` | `game` | **sin core oficial** |
| `*.sol` / `foundry.toml` / `hardhat.config.*` | `web3` | **sin core oficial** |

## 🧭 Matriz 3 — Desempate

| Conflicto | Resolución | Razón |
|---|---|---|
| Python con FastAPI **y** pandas | `backend` | El API es el contrato del proyecto; el data stack es un detalle interno |
| Python solo con pandas/langchain | `python` | No hay servidor; el core de backend aportaría rituales sin superficie API |
| `package.json` con `react` **y** `express` en el mismo repo | `web` (recomendada) + `backend-agent-rules` como alternativa | Un solo `.agents/` es posible; la alternativa se documenta para cuando el backend se separe |
| Repo con `apps/web` + `apps/api` + `apps/mobile` | Elegí la del producto principal y **decí explícitamente** que el resto necesita otro repo | No existe core multicore publicado en la organización |
| `pubspec.yaml` sin dependencia `flutter` ( Dart puro ) | `flutter` si hay `lib/main.dart` + `android/` o `ios/`; si no, `backend` | Un paquete Dart puro no es un proyecto Flutter |
| Sin ningún marcador reconocible | **No recomiendes nada** | Decí que el stack no se pudo determinar y preguntá cuál es. Inventar un core es peor que preguntar |

## 🧭 Matriz 4 — ¿Existe el repo?

| Resultado de `git ls-remote` / `gh api` | Decisión |
|---|---|
| Responde con refs | **Existe** → ofrecelo |
| No responde / 404 | **No existe** → no lo ofrezcas. Menciónalo solo como "previsto pero no publicado" |
| Sin red | Usá `knowledge/catalogo_gobernanzas.md` (verificado a mano) y marcá el resultado como *offline* |

> La razón de este paso: el `README.md` del repositorio núcleo lista gobernanzas y skills que todavía no están en GitHub (`game-agent-rules`, `multicore-agent-rules`, `solidity-agent-rules`, `second-brain-agent-rules` y todas las skills de Python/Kotlin/Swift). Recomendar cualquiera de ellas produce un fallo de instalación.

## 🧭 Matriz 5 — Skills candidatas

| Evidencia en el proyecto | Skill candidata | Alias |
|---|---|---|
| `firebase_core` / `firebase-auth` en deps | `firebase-auth-agent-skill` | `$firebaseauth` |
| `bloc` / `flutter_bloc` / `provider` en deps (Flutter) | `flutter-bloc-patterns-agent-skill` | `$bloc` |
| `stripe` / `in_app_payments` en deps | `flutter-payments-agent-skill` / `backend-stripe-agent-skill` | `$pay` / `$stripe` |
| `react` / `next` en deps | `web-react-agent-skill` | `$react` |
| `svelte` / `@sveltejs/kit` en deps | `web-svelte-agent-skill` | `$svelte` |
| `three` en deps | `three-js-agent-skills` | `$threejs` |
| `ws` / `socket.io` / `socket` en deps | `web-realtime-agent-skill` | `$realtime` |
| `apollo` / `graphql` en deps | `backend-graphql-agent-skill` | `$gql` |
| `passport` / `next-auth` / `jwt` / `oauth` en deps | `backend-auth-oauth-agent-skill` | `$auth` |
| `wordpress` / `wp-json` en deps o `composer.json` con wordpress | `wordpress-agent-skill` | `$wp` |
| `Dockerfile` / `docker-compose.yml` / `.github/workflows/` | `infra-agent-skill` | `$infra` |
| `logging` / `opentelemetry` / `sentry` en deps | `monitoring-agent-skill` | `$monitoring` |
| `analytics` / `crashlytics` / `sentry` de producto | `telemetry-agent-skill` | `$telemetry` |
| `i18n` / `intl` / `flutter_localizations` / `next-intl` | `i18n-agent-skill` | `$i18n` |
| Siempre (default razonable) | `security-agent-skill` | `$security` |

> La skill transversal `security-agent-skill` casi siempre aplica: es agnóstica y audita secretos y OWASP. Se lista sin interrogantes en todos los casos.

## 🧭 Matriz 6 — Fallos durante la instalación

| Síntoma | Causa probable | Acción |
|---|---|---|
| `fatal: not a git repository` | El proyecto no es repo git | `git init` en la raíz y reintentar |
| `destination path '.agents' already exists` | Ya hay un `.agents/` no gestionado | **No borrar.** Reportar y preguntar al usuario |
| `Permission denied (publickey)` | Se intentó SSH | Usar la URL HTTPS: `https://github.com/Agent-Rules-Ecosystem/<repo>.git` |
| `could not read Username` | Credenciales HTTPS no configuradas | `gh auth setup-git` y reintentar una vez |
| `fatal: remote error: upload-pack: not our ref` | Ref del submodule inexistente en el remoto | `git submodule sync` y reintentar |
| `submodule status` muestra prefijo `-` | Módulo no inicializado | `git submodule update --init --recursive` |
| El repo no responde en `ls-remote` | Repo no publicado | No ofrecerlo. Volver al Paso 2 |

## 🧭 Matriz 7 — Writing discipline

| Se pide… | Dónde | Nunca |
|---|---|---|
| Instalar gobernanza | `.agents/` (submódulo) | Nunca crear archivos sueltos dentro de `.agents/` |
| Instalar skill | `.skill/<repo-completo>/` (submódulo) | Nunca dentro de `.agents/` ni con nombre corto |
| Track del proyecto | `overview/` | Nunca en `.agents/` ni en `.skill/` |
| Aprendizaje del flujo | `overview/learning.md` | Nunca editando `.agents/` (Protocolo de 3 Vías) |

## 🧭 Matriz 8 — Límites de escritura en `overview/`

| Acción | Permitido | Prohibido |
|---|---|---|
| Crear archivos que faltan | Sí, copiando desde `.agents/templates/` | Crear contenido propio (tareas, deuda, notas) |
| Modificar archivos existentes | **Nunca** en la ruta de proyecto ya gobernado | Reescribir `session.md`, `work.md`, `tasks.md`, `work_review.md` |
| Mover ítems entre secciones | No | Mover `w1` a "Completados" sin que el ritual lo haga |
| Registrar hallazgos del código | No | Crear pendientes o deuda a partir de una lectura del repo |
| Commitear | No | `git commit` sin pedido explícito |

> Por qué tantoroyego: `overview/` es el estado compartido entre el agente y la persona que trabaja en el proyecto. Un agente que "completa" el estado introduce entradas que nadie escribió ni revisó, y el primer `$boot` del usuario las encuentra como si fueran reales. Escribir de más en el track es peor que dejar el track incompleto: lo incompleto se completa con `$boot`, lo falso no se distingue.

## 🧭 Matriz 9 — Momento de la pregunta

| Situación | Preguntá | Instalá |
|---|---|---|
| "instalala" / "dale" | No hace falta | Sí |
| "qué me recomendás" | Sí | No |
| "solo el listado, no instales" | No hace falta (ya dijo que no) | No |
| "quiero el track vivo" | Sí | No |
| "instalame la skill X" | No hace falta | Sí, solo esa skill |
| Mensaje con "instalá" pero sin especificar cuál | Sí, cuál | No |
| El usuario ya confirmó en un mensaje anterior | No hace falta | Sí |
