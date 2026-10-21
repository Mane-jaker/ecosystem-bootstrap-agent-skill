# Skills candidatas

Cómo armar la lista de skills que se **mencionan** (no se instalan) para un proyecto dado.

---

## 🎯 Principio

Una skill es candidata si su **evidencia aparece en el proyecto**. El dominio solo abre el menú; la dependencia concreta es la que la justifica. `flutter-bloc-patterns-agent-skill` no aplica a un proyecto Flutter que no usa BLoC ni Provider: forzarla agrega ruido al contexto del agente.

Excepción: las skills transversales de auditoría de calidad (`security`, `performance`, `monitoring`) tienen valor por defecto en la mayoría de los proyectos, porque auditan lo que ya está y no exigen migración de código.

---

## 📋 Matriz por evidencia

| Evidencia en el proyecto | Skill candidata | Alias | Instalación |
|---|---|---|---|
| `firebase_core`, `firebase_auth`, `firebase.json` | `firebase-auth-agent-skill` | `$firebaseauth` | `git submodule add https://github.com/Agent-Rules-Ecosystem/firebase-auth-agent-skill.git .skill/firebase-auth-agent-skill` |
| `flutter_bloc`, `bloc`, `provider`, `riverpod` | `flutter-bloc-patterns-agent-skill` | `$bloc` | `.../flutter-bloc-patterns-agent-skill.git .skill/flutter-bloc-patterns-agent-skill` |
| `stripe`, `in_app_payments`, `purchases_flutter` | `flutter-payments-agent-skill` / `backend-stripe-agent-skill` | `$pay` / `$stripe` | `.../flutter-payments-agent-skill.git .skill/flutter-payments-agent-skill` |
| `odoo` + firebase en el mismo proyecto | `flutter-firebase-odoo-agent-skill` | `$odoo` | `.../flutter-firebase-odoo-agent-skill.git .skill/flutter-firebase-odoo-agent-skill` |
| `react`, `next` | `web-react-agent-skill` | `$react` | `.../web-react-agent-skill.git .skill/web-react-agent-skill` |
| `svelte`, `@sveltejs/kit` | `web-svelte-agent-skill` | `$svelte` | `.../web-svelte-agent-skill.git .skill/web-svelte-agent-skill` |
| `three`, `@react-three/fiber`, `babylonjs` | `three-js-agent-skills` | `$threejs` | `.../three-js-agent-skills.git .skill/three-js-agent-skills` |
| `ws`, `socket.io`, `socket`, `eventsource` | `web-realtime-agent-skill` | `$realtime` | `.../web-realtime-agent-skill.git .skill/web-realtime-agent-skill` |
| `graphql`, `@apollo/client`, `apollo-server` | `backend-graphql-agent-skill` | `$gql` | `.../backend-graphql-agent-skill.git .skill/backend-graphql-agent-skill` |
| `passport`, `next-auth`, `@auth/core`, `jose`, `jsonwebtoken` | `backend-auth-oauth-agent-skill` | `$auth` | `.../backend-auth-oauth-agent-skill.git .skill/backend-auth-oauth-agent-skill` |
| `wordpress`, `wp-json`, `composer.json` con wordpress | `wordpress-agent-skill` | `$wp` | `.../wordpress-agent-skill.git .skill/wordpress-agent-skill` |
| `Dockerfile`, `docker-compose.yml`, `k8s/`, `.github/workflows/` | `infra-agent-skill` | `$infra` | `.../infra-agent-skill.git .skill/infra-agent-skill` |
| `logging`, `opentelemetry`, `otel`, `sentry`, `datadog` | `monitoring-agent-skill` | `$monitoring` | `.../monitoring-agent-skill.git .skill/monitoring-agent-skill` |
| `analytics`, `amplitude`, `mixpanel`, `crashlytics`, `firebase_crashlytics` | `telemetry-agent-skill` | `$telemetry` | `.../telemetry-agent-skill.git .skill/telemetry-agent-skill` |
| `i18n`, `intl`, `flutter_localizations`, `next-intl`, `i18next` | `i18n-agent-skill` | `$i18n` | `.../i18n-agent-skill.git .skill/i18n-agent-skill` |
| `modbus`, `plc` (automatización industrial) | `go-plc-modbus-agent-skill` / `rust-plc-modbus-agent-skill` | `$goplc` / `$rustplc` | `.../go-plc-modbus-agent-skill.git .skill/go-plc-modbus-agent-skill` |
| `steam`, `steamworks` (juego) | `godot-steamworks-agent-skill` | `$steam` | `.../godot-steamworks-agent-skill.git .skill/godot-steamworks-agent-skill` |
| Proyecto con capas a analizar / auditoría de Clean Arch | `grapho-agent-skill` | `$grapho` | `.../grapho-agent-skill.git .skill/grapho-agent-skill` |
| **Default razonable** | `security-agent-skill` | `$security` | `.../security-agent-skill.git .skill/security-agent-skill` |
| Tareas de performance, bundles, frame budget | `performance-agent-skill` | `$perf` | `.../performance-agent-skill.git .skill/performance-agent-skill` |

---

## 🚦 Reglas de interacción

1. **Se listan, no se preguntan.** Mostrá la tabla y seguí con el reporte. No abras una pregunta por skills.
2. **Sin pedido explícito, no se instalan.** Si el usuario no dice nada, la lista queda como información.
3. **Verificá existencia antes de listar.** Mismo check que para las gobernanzas (`git ls-remote` / `gh api`). Skills prometidas pero no publicadas no se listan.
4. **Mencioná las skills inline del core.** Vienen con `.agents/` y no se instalan: `flutter-clean-arch` y `mermaid-diagrams` (flutter), `mermaid-diagrams` (web).
5. **Priorizá.** Si hay más de 6 candidatas, ordená por evidencia directa y dejá las transversales de default al final.
6. **No dupliques con las del core.** Si `.agents/skills/README.md` ya lista una skill, esa es la fuente de verdad para el core instalado.

---

## 📄 Formato de la tabla en el reporte

| Skill | Alias | Por qué aplica | Instalación |
|---|---|---|---|
| `firebase-auth-agent-skill` | `$firebaseauth` | `firebase_core` y `firebase_auth` en `pubspec.yaml` | `git submodule add https://github.com/Agent-Rules-Ecosystem/firebase-auth-agent-skill.git .skill/firebase-auth-agent-skill` |
| `web-realtime-agent-skill` | `$realtime` | `ws` en `package.json` | `...` |
| `security-agent-skill` | `$security` | Auditoría de secretos y OWASP (transversal) | `...` |

Colocá el comando completo en la última columna: el usuario debería poder copiarlo sin reconstruir nada.
