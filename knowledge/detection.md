# Detección de stack

Cómo determinar el dominio del proyecto leyendo evidencia. La regla general: **el marcador de archivo pesa más que la palabra del usuario**, y un dominio que no se puede determinar no se inventa.

---

## ⚖️ Pesos

| Evidencia | Peso | Comentario |
|---|---|---|
| Archivo marcador en la raíz (`pubspec.yaml`, `go.mod`, `Cargo.toml`, `Package.swift`, `build.gradle.kts`) | 2 | Presencia del archivo, sin importar el contenido |
| Dependencia de framework en el manifiesto (`package.json`, `pubspec.yaml`, `pyproject.toml`, `go.mod`, `Cargo.toml`) | 2 | La señal más fuerte del dominio |
| Directorio dedicado (`lib/`, `android/`, `ios/`, `src/`, `app/`) | 1 | Orientativo |
| Extensión de código fuente dominante (`.dart`, `.tsx`, `.ts`, `.kt`, `.swift`, `.rs`, `.go`, `.py`) | 1 | Contar archivos, no bytes |
| Archivo de framework de la plataforma (`web/`, `manifest.json`, `Info.plist`, `docker-compose.yml`) | 1 | Complementario |

**Dominio elegido** = el de mayor score. Si dos empatan, aplica la matriz de desempate de `core/brain.md`.

---

## 🔍 Señales por dominio

### Flutter
| Señal | Detalle |
|---|---|
| `pubspec.yaml` | weight 2 si contiene `flutter:` en `dependencies:` o `environment: sdk: flutter` |
| `lib/main.dart` | weight 1 |
| `android/` + `ios/` | weight 1 (proyecto de plataforma, no paquete Dart) |
| Dependencias típicas | `flutter_bloc`, `provider`, `go_router`, `dio`, `freezed`, `riverpod` |

**Trampa**: un paquete Dart puro (sin `flutter`) no es Flutter. Si `pubspec.yaml` existe pero no menciona `flutter` y no hay `android/`/`ios/`, es un proyecto Dart/backend.

### Web / Frontend
| Señal | Detalle |
|---|---|
| `package.json` con `react` / `next` / `vue` / `svelte` / `@sveltejs/kit` / `astro` / `nuxt` / `@angular/core` | weight 2 |
| `src/app/`, `app/`, `pages/`, `components/` | weight 1 |
| `index.html` + bundler (`vite.config.*`, `webpack.config.*`, `next.config.*`, `svelte.config.*`) | weight 2 (confirma web) |
| Config de Tailwind (`tailwind.config.*`) | weight 1 (solo web) |

**Trampa**: `package.json` con `express` y sin framework de UI es **backend**, no web.

### Backend
> El core `backend-agent-rules` cubre **Node/TS y Python**. Para Go y Rust existen cores más específicos (`go-agent-rules`, `rust-agent-rules`): un servicio Go es dominio `go`, no `backend`.

| Señal | Detalle |
|---|---|
| `package.json` con `express` / `fastify` / `@nestjs/*` / `koa` / `hapi` | backend (3) |
| `pyproject.toml`/`requirements.txt` con `fastapi` / `django` / `flask` / `starlette` / `uvicorn` | backend (3) |
| Directorio `api/`, `server/`, `src/api/`, `controllers/`, `routers/` | weight 1 |
| `Dockerfile` con `EXPOSE` y comando de servidor | weight 1 |

### Go
| Señal | Detalle |
|---|---|
| `go.mod` | weight 2 |
| `cmd/`, `internal/`, `pkg/` | weight 1 |
| Framework web (`gin`, `echo`, `chi`, `fiber`) | **no cambia el dominio**: sigue siendo `go` → `go-agent-rules` |

### Kotlin
| Señal | Detalle |
|---|---|
| `build.gradle.kts` / `settings.gradle.kts` | weight 2 |
| `gradle/libs.versions.toml` | weight 1 |
| Archivos `*.kt` | weight 1 |
| `android/` con `AndroidManifest.xml` | weight 1 |

### Python
| Señal | Detalle |
|---|---|
| `pyproject.toml` / `requirements.txt` / `Pipfile` / `setup.py` | weight 2 |
| `pandas` / `numpy` / `langchain` / `airflow` / `polars` / `torch` | python (2) |
| `fastapi` / `django` / `flask` / `starlette` | backend (2) |
| `*.ipynb`, `scripts/`, `etl/` | python (1) |
| `pytest.ini`, `conftest.py` | weight 1 |

### Rust
| Señal | Detalle |
|---|---|
| `Cargo.toml` | weight 2 |
| `src/main.rs`, `src/lib.rs` | weight 1 |
| `axum` / `actix-web` | **no cambia el dominio**: sigue siendo `rust` → `rust-agent-rules` |

### Swift
| Señal | Detalle |
|---|---|
| `Package.swift` | weight 2 |
| `*.xcodeproj` / `*.xcworkspace` | weight 2 |
| `Sources/` + `Tests/` con `*.swift` | weight 1 |
| `ios/` con `Info.plist` | weight 1 |

### Sin core oficial publicado
| Señal | Dominio | Decisión |
|---|---|---|
| `project.godot` | game | Reportar: `game-agent-rules` está en el manifiesto del ecosistema pero **no está publicado** |
| `*.sol`, `foundry.toml`, `hardhat.config.*` | web3 | Reportar: `solidity-agent-rules` **no está publicado** |
| Repo con varias tecnologías sin claro principal | multicore | Reportar: `multicore-agent-rules` **no está publicado**; elegir la del stack principal |

---

## 🔀 Casos borde frecuentes

### Frontend + backend en el mismo repo
Detectá los dos dominios. `.agents/` admite **una sola** gobernanza, así que:
1. Si el backend es una API interna que consume el front → recomendá `web-agent-rules` (el core web ya cubre la capa de datos del cliente) y mencioná `backend-agent-rules` como alternativa si la API se independiza.
2. Si son servicios separados (`apps/web`, `apps/api`) → recomendá la del producto principal y explicitá que la segunda necesita su propio repo o un core multicore (no publicado).

### Monorepo con mobile + web
Elige la plataforma que concentra el trabajo. Mencioná la otra como alternativa.

### Proyecto vacío (recién creado, sin archivos)
No hay señales: **preguntá** cuál es el stack en vez de recomendar a ciegas. Si el usuario ya lo dijo en el mensaje, tomá su declaración como evidencia.

### Proyecto que ya tiene `.agents/`
No detectes ni recomiendes: ya hay gobernanza. Short-circuit.

### Detección de skills candidatas
Una vez elegido el dominio, las skills candidatas se derivan de las **dependencias**, no del dominio solo. Ejemplo: un proyecto Flutter sin `firebase_core` no necesita `$firebaseauth` aunque el dominio sea Flutter. Reglas completas en `skills_candidatas.md`.
