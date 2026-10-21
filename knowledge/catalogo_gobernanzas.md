# Catálogo de gobernanzas y skills

## 🔁 Fuente de verdad: runtime

Este archivo es el **respaldo offline**. La fuente de verdad es el repo núcleo del ecosistema, para que la skill no se pudra cuando la organización agregue o renombre repos.

```bash
curl -sL https://raw.githubusercontent.com/Agent-Rules-Ecosystem/agent-rules-ecosystem/main/ESTRUCTURA_CLONACION.md
```

De ese documento se extraen dos clases de repos:

| Patrón | Clase | Dominio |
|---|---|---|
| `[a-z0-9][a-z0-9-]*-agent-rules` | Gobernanza | el nombre sin el sufijo (`flutter-agent-rules` → `flutter`) |
| `[a-z0-9][a-z0-9-]*-agent-skills?` | Skill | la primera palabra del nombre (`web-react-agent-skill` → `web`) |

Después, **verificá existencia** antes de ofrecer:

```bash
git ls-remote --exit-code -h https://github.com/Agent-Rules-Ecosystem/<repo>.git >/dev/null 2>&1 \
  && echo "EXISTS $repo" || echo "MISSING $repo"
```

Alternativa sin git en PATH: `gh api repos/Agent-Rules-Ecosystem/<repo> --jq .name`.

---

## 🛡️ Gobernanzas (`*-agent-rules`) — estado verificado

Verificado contra la API de GitHub de la organización. **Son las 8 que existen hoy.**

| Dominio | Repo | Estado | URL |
|---|---|---|---|
| flutter | `flutter-agent-rules` | ✅ publicado | `https://github.com/Agent-Rules-Ecosystem/flutter-agent-rules.git` |
| web | `web-agent-rules` | ✅ publicado | `https://github.com/Agent-Rules-Ecosystem/web-agent-rules.git` |
| backend | `backend-agent-rules` | ✅ publicado | `https://github.com/Agent-Rules-Ecosystem/backend-agent-rules.git` |
| go | `go-agent-rules` | ✅ publicado | `https://github.com/Agent-Rules-Ecosystem/go-agent-rules.git` |
| kotlin | `kotlin-agent-rules` | ✅ publicado | `https://github.com/Agent-Rules-Ecosystem/kotlin-agent-rules.git` |
| python | `python-agent-rules` | ✅ publicado | `https://github.com/Agent-Rules-Ecosystem/python-agent-rules.git` |
| rust | `rust-agent-rules` | ✅ publicado | `https://github.com/Agent-Rules-Ecosystem/rust-agent-rules.git` |
| swift | `swift-agent-rules` | ✅ publicado | `https://github.com/Agent-Rules-Ecosystem/swift-agent-rules.git` |

### 📋 En el manifiesto pero NO publicados

Estos repos aparecen en el README y en `ESTRUCTURA_CLONACION.md` del núcleo, pero **no existen en GitHub**. No los ofrezcas como instalables; si el proyecto cae en ese dominio, decilo explícitamente.

| Repo | Dominio | Estado |
|---|---|---|
| `game-agent-rules` | game | ❌ no publicado |
| `multicore-agent-rules` | multicore | ❌ no publicado |
| `solidity-agent-rules` | web3 | ❌ no publicado |
| `second-brain-agent-rules` | secondbrain | ❌ no publicado |

---

## ⚡ Skills (`*-agent-skill`) — estado verificado

| Repo | Alias `$` | Dominio | Estado |
|---|---|---|---|
| `flutter-bloc-patterns-agent-skill` | `$bloc` | flutter | ✅ |
| `flutter-payments-agent-skill` | `$pay` | flutter | ✅ |
| `flutter-firebase-odoo-agent-skill` | `$odoo` | flutter | ✅ |
| `web-react-agent-skill` | `$react` | web | ✅ |
| `web-svelte-agent-skill` | `$svelte` | web | ✅ |
| `web-realtime-agent-skill` | `$realtime` | web | ✅ |
| `three-js-agent-skills` | `$threejs` | web | ✅ |
| `wordpress-agent-skill` | `$wp` | web | ✅ |
| `backend-auth-oauth-agent-skill` | `$auth` | backend | ✅ |
| `backend-graphql-agent-skill` | `$gql` | backend | ✅ |
| `backend-stripe-agent-skill` | `$stripe` | backend | ✅ |
| `go-plc-modbus-agent-skill` | `$goplc` | go | ✅ |
| `godot-steamworks-agent-skill` | `$steam` | game | ✅ |
| `godot-firebase-agent-skill` | `$godotfire` | game | ✅ |
| `godot-mobile-monetization-agent-skill` | `$mobile` | game | ✅ |
| `godot-dialogue-plugin-agent-skill` | `$dialogue` | game | ✅ |
| `godot-nakama-agent-skill` | `$nakama` | game | ✅ |
| `firebase-auth-agent-skill` | `$firebaseauth` | transversal | ✅ |
| `grapho-agent-skill` | `$grapho` | transversal | ✅ |
| `i18n-agent-skill` | `$i18n` | transversal | ✅ |
| `infra-agent-skill` | `$infra` | transversal | ✅ |
| `monitoring-agent-skill` | `$monitoring` | transversal | ✅ |
| `performance-agent-skill` | `$perf` | transversal | ✅ |
| `security-agent-skill` | `$security` | transversal | ✅ |
| `telemetry-agent-skill` | `$telemetry` | transversal | ✅ |

### 📋 En el manifiesto pero NO publicadas

| Repo | Dominio | Estado |
|---|---|---|
| `python-fastapi-pydantic-agent-skill` | python | ❌ no publicado |
| `python-langchain-agents-agent-skill` | python | ❌ no publicado |
| `python-pandas-data-agent-skill` | python | ❌ no publicado |
| `python-blender-addon-agent-skill` | python | ❌ no publicado |
| `kotlin-jetpack-compose-agent-skill` | kotlin | ❌ no publicado |
| `kotlin-coroutines-flow-agent-skill` | kotlin | ❌ no publicado |
| `kotlin-room-sqlite-agent-skill` | kotlin | ❌ no publicado |
| `swiftui-navigation-state-agent-skill` | swift | ❌ no publicado |
| `swift-async-concurrency-agent-skill` | swift | ❌ no publicado |
| `swift-swiftdata-realm-agent-skill` | swift | ❌ no publicado |
| `grapho-3d-visualizer-agent-skill` | transversal | ❌ no publicado |

### 🧩 Skills inline (ya vienen con el core)

Los cores `*-agent-rules` traen skills propias dentro de `.agents/skills/`. **No se instalan**: vienen con la gobernanza.

| Core | Skills inline |
|---|---|
| `flutter-agent-rules` | `flutter-clean-arch`, `mermaid-diagrams` |
| `web-agent-rules` | `mermaid-diagrams` |

---

## 🔤 Nota sobre renombres

Dos nombres del estándar apuntan al mismo repo (GitHub redirige):

| Nombre en el estándar | Repo canónico |
|---|---|
| `web-svelte-patterns-agent-skill` | `web-svelte-agent-skill` |
| `flutter-firebase-auth-agent-skill` | `firebase-auth-agent-skill` |

Instalá siempre con el **nombre canónico** del repo, que es el que aparece en la columna "Repo" de las tablas de arriba.
