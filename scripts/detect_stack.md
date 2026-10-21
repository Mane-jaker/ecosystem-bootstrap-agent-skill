# Script: detección rápida de stack

> **Fast path** del Paso 1 de la skill. Escanea la raíz del proyecto y devuelve un score por dominio. No hace red, no escribe nada, no modifica el proyecto.
>
> Por el estándar 100% Markdown del ecosistema, el script vive como bloque de código en este archivo. Para ejecutarlo, escribí el bloque en un archivo temporal y corrélo.

## Uso

```bash
# 1. Extraer el bloque a un temporal y ejecutarlo contra la raíz del proyecto
bash detect_stack.sh /ruta/al/proyecto

# Salida esperada (una línea por dominio detectado, ordenada por score):
# STACK=flutter SCORE=4 EVIDENCE=pubspec.yaml(flutter);lib/main.dart;android/,ios/
# STACK=web SCORE=2 EVIDENCE=package.json(react)
```

Código del script:

```bash
#!/usr/bin/env bash
# detect_stack.sh — deteccion de stack para el Agent-Rules-Ecosystem
# Uso: bash detect_stack.sh [ruta-del-proyecto]
# Salida: STACK=<dominio> SCORE=<n> EVIDENCE=<...>

ROOT="${1:-.}"
cd "$ROOT" || { echo "ERROR: no se puede entrar a $ROOT" >&2; exit 1; }

emit() { # emit <dominio> <score> <evidencia>
  [ "$2" -gt 0 ] && printf 'STACK=%s SCORE=%s EVIDENCE=%s\n' "$1" "$2" "${3#;}"
}

has()  { [ -e "$1" ]; }
hasd() { [ -d "$1" ]; }
dep()  { # dep <archivo> <regex>
  [ -f "$1" ] && grep -Eiq "$2" "$1" && return 0
  return 1
}
count_ext() { # count_ext <ext> <minimo>
  local n
  n=$(find . -name "*.$1" -not -path './.git/*' -not -path './.agents/*' -not -path './.skill/*' 2>/dev/null | head -100 | wc -l | tr -d ' ')
  [ "$n" -ge "$2" ] && echo "$n" || echo 0
}

# ---------------- FLUTTER ----------------
s=0; e=""
if dep pubspec.yaml '(^|[^a-z])flutter([^a-z]|$)'; then
  s=$((s+2)); e="$e;pubspec.yaml(flutter)"
elif has pubspec.yaml; then
  s=$((s+1)); e="$e;pubspec.yaml(sin flutter)"
fi
has lib/main.dart && { s=$((s+1)); e="$e;lib/main.dart"; }
{ hasd android || hasd ios; } && { s=$((s+1)); e="$e;android/,ios/"; }
emit flutter "$s" "$e"

# ---------------- WEB ----------------
s=0; e=""
dep package.json '"(react|next|vue|svelte|astro|nuxt|@angular/core|@sveltejs/kit)"' && { s=$((s+2)); e="$e;package.json(framework)"; }
{ has index.html || hasd src/app || hasd pages || hasd components; } && { s=$((s+1)); e="$e;frontend dirs"; }
ls vite.config.* next.config.* svelte.config.* astro.config.* nuxt.config.* 2>/dev/null | head -1 | grep -q . && { s=$((s+1)); e="$e;bundler config"; }
emit web "$s" "$e"

# ---------------- BACKEND (Node/TS y Python web API) ----------------
# Los servidores de Go y Rust NO cuentan como backend: existen/go-agent-rules y
# rust-agent-rules, que son mas especificos. Un servicio Go es 'go', no 'backend'.
s=0; e=""
dep package.json '"(express|fastify|@nestjs/core|koa|hapi)"' && { s=$((s+3)); e="$e;package.json(server)"; }
PY_SERVER=0
dep pyproject.toml '(fastapi|django|flask|starlette|uvicorn|gunicorn)'   && { s=$((s+3)); e="$e;pyproject(server)"; PY_SERVER=1; }
dep requirements.txt '(fastapi|django|flask|starlette|uvicorn|gunicorn)'  && { s=$((s+3)); e="$e;requirements(server)"; PY_SERVER=1; }
{ hasd api || hasd server || hasd src/api || hasd controllers; } && { s=$((s+1)); e="$e;api dirs"; }
emit backend "$s" "$e"

# ---------------- GO ----------------
s=0; e=""
has go.mod && { s=$((s+2)); e="$e;go.mod"; }
{ hasd cmd || hasd internal || hasd pkg; } && { s=$((s+1)); e="$e;go dirs"; }
emit go "$s" "$e"

# ---------------- KOTLIN ----------------
s=0; e=""
{ has build.gradle.kts || has settings.gradle.kts; } && { s=$((s+2)); e="$e;gradle kts"; }
has gradle/libs.versions.toml && { s=$((s+1)); e="$e;version catalog"; }
n=$(count_ext kt 3); [ "$n" -gt 0 ] && { s=$((s+1)); e="$e;$n .kt"; }
emit kotlin "$s" "$e"

# ---------------- PYTHON (datos/IA, sin servidor web) ----------------
s=0; e=""
{ has pyproject.toml || has requirements.txt || has Pipfile || has setup.py; } && { s=$((s+2)); e="$e;python manifest"; }
if [ "$PY_SERVER" -eq 0 ]; then
  dep pyproject.toml '(pandas|numpy|langchain|airflow|polars|torch|scikit-learn)'  && { s=$((s+2)); e="$e;pyproject(data/ai)"; }
  dep requirements.txt '(pandas|numpy|langchain|airflow|polars|torch|scikit-learn)' && { s=$((s+2)); e="$e;requirements(data/ai)"; }
fi
n=$(count_ext py 3); [ "$n" -gt 0 ] && { s=$((s+1)); e="$e;$n .py"; }
emit python "$s" "$e"

# ---------------- RUST ----------------
s=0; e=""
has Cargo.toml && { s=$((s+2)); e="$e;Cargo.toml"; }
{ has src/main.rs || has src/lib.rs; } && { s=$((s+1)); e="$e;rust src"; }
emit rust "$s" "$e"

# ---------------- SWIFT ----------------
s=0; e=""
{ has Package.swift || ls *.xcodeproj *.xcworkspace 2>/dev/null | head -1 | grep -q .; } && { s=$((s+2)); e="$e;swift manifest"; }
{ hasd ios && has ios/Info.plist; } && { s=$((s+1)); e="$e;ios/"; }
n=$(count_ext swift 3); [ "$n" -gt 0 ] && { s=$((s+1)); e="$e;$n .swift"; }
emit swift "$s" "$e"

# ---------------- SIN CORE OFICIAL ----------------
s=0; e=""
has project.godot && { s=$((s+2)); e="$e;project.godot"; }
emit game "$s" "$e"

s=0; e=""
{ has foundry.toml || has hardhat.config.js || has hardhat.config.ts; } && { s=$((s+2)); e="$e;web3 tooling"; }
n=$(count_ext sol 1); [ "$n" -gt 0 ] && { s=$((s+1)); e="$e;$n .sol"; }
emit web3 "$s" "$e"

# ---------------- SKILLS CANDIDATAS (por dependencia) ----------------
echo "---"
echo "CANDIDATE_SKILLS"
dep pubspec.yaml '(firebase_core|firebase_auth)'        && echo "  firebase-auth-agent-skill (\$firebaseauth)"
dep pubspec.yaml '(flutter_bloc|bloc:|provider:|riverpod)' && echo "  flutter-bloc-patterns-agent-skill (\$bloc)"
dep pubspec.yaml '(stripe|in_app_payments)'               && echo "  flutter-payments-agent-skill (\$pay)"
dep package.json '"(react|next)"'                         && echo "  web-react-agent-skill (\$react)"
dep package.json '"(svelte|@sveltejs/kit)"'               && echo "  web-svelte-agent-skill (\$svelte)"
dep package.json '"(three|@react-three/fiber)"'           && echo "  three-js-agent-skills (\$threejs)"
dep package.json '"(ws|socket.io|socket-client)"'         && echo "  web-realtime-agent-skill (\$realtime)"
dep package.json '"(graphql|@apollo/client)"'             && echo "  backend-graphql-agent-skill (\$gql)"
dep package.json '"(next-auth|@auth/core|jose|jsonwebtoken|passport)"' && echo "  backend-auth-oauth-agent-skill (\$auth)"
dep package.json '"stripe"'                               && echo "  backend-stripe-agent-skill (\$stripe)"
{ has Dockerfile || has docker-compose.yml || hasd .github/workflows; } && echo "  infra-agent-skill (\$infra)"
dep package.json '"(opentelemetry|otel|sentry|datadog|log4js|winston)"' && echo "  monitoring-agent-skill (\$monitoring)"
dep package.json '"(amplitude|mixpanel|segment|analytics)"' && echo "  telemetry-agent-skill (\$telemetry)"
dep pubspec.yaml '(intl|flutter_localizations)'           && echo "  i18n-agent-skill (\$i18n)"
echo "  security-agent-skill (\$security)"

echo "---"
echo "NEXT: verificar existencia de cada repo con 'git ls-remote --exit-code -h https://github.com/Agent-Rules-Ecosystem/<repo>.git' antes de ofrecerlo"
```

## Notas de uso

| Nota | Detalle |
|---|---|
| Excluye `.git`, `.agents` y `.skill` del conteo | El submodule de gobernanza contiene `.md` y no debe contaminar la detección |
| El bloque `CANDIDATE_SKILLS` es orientativo | Cada skill sigue requiriendo verificación de existencia y evidencia propia |
| Los dominios `game` y `web3` no tienen core publicado | Se emiten para que el agente pueda reportar la ausencia de gobernanza oficial |
| La salida no decide el dominio final | Aplicá las matrices de desempate de `core/brain.md` |
