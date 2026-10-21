# Formato del informe (Paso 3)

Plantilla del informe que se muestra **antes** de instalar. Máximo ~20 líneas: el objetivo es que el usuario confirme rápido, no que lea un documento.

---

## 📋 Estructura

```markdown
## 🔎 Análisis del proyecto

**Stack detectado:** <dominio> — <evidencia principal>
- `<archivo>`: <señal>
- `<manifiesto>`: <dependencia>

**Gobernanza recomendada:** `<repo>` (`<dominio>`)
<una línea de por qué: qué rituales y qué conocimiento aporta a este proyecto>

**Alternativas:**
- `<repo>` — <condición de applicability>

<solo si aplica>
**Sin core oficial:** <dominio detectado> → `<repo>` figura en el manifiesto del ecosistema pero no está publicado.

**Skills compatibles con este proyecto** (mencionadas, no instaladas):
| Skill | Alias | Por qué aplica | Instalación |
|---|---|---|---|
| `<repo>` | `$alias` | <evidencia> | `git submodule add ... ` |
```

---

## ✅ Ejemplo real

```markdown
## 🔎 Análisis del proyecto

**Stack detectado:** flutter — `pubspec.yaml` con `flutter` en `dependencies`
- `pubspec.yaml`: `flutter_bloc`, `firebase_core`, `go_router`
- `lib/`: 34 archivos `.dart`, estructura por feature

**Gobernanza recomendada:** `flutter-agent-rules`
Aporta los rituales `$boot`/`$work`/`$close` con estado persistente en `overview/`, el checklist de release `.aab` y las skills inline de Clean Architecture y diagramas Mermaid.

**Alternativas:**
- `web-agent-rules` — solo si este proyecto llegara a tener superficie web (PWA / panel admin).

**Skills compatibles con este proyecto** (mencionadas, no instaladas):
| Skill | Alias | Por qué aplica | Instalación |
|---|---|---|---|
| `flutter-bloc-patterns-agent-skill` | `$bloc` | `flutter_bloc` en `pubspec.yaml` | `git submodule add https://github.com/Agent-Rules-Ecosystem/flutter-bloc-patterns-agent-skill.git .skill/flutter-bloc-patterns-agent-skill` |
| `firebase-auth-agent-skill` | `$firebaseauth` | `firebase_core` en `pubspec.yaml` | `git submodule add https://github.com/Agent-Rules-Ecosystem/firebase-auth-agent-skill.git .skill/firebase-auth-agent-skill` |
| `security-agent-skill` | `$security` | auditoría de secretos y OWASP (transversal) | `git submodule add https://github.com/Agent-Rules-Ecosystem/security-agent-skill.git .skill/security-agent-skill` |
```

---

## 🧭 Reglas de escritura

| Regla | Motivo |
|---|---|
| Mostrá la evidencia (archivo + dependencia), no solo el veredicto | Permite corregirte; un stack mal detectado arruina la instalación |
| Una sola recomendación | `.agents/` admite una gobernanza; presentar tres opciones sin jerarquía confunde |
| Incluí el comando completo de cada skill | El usuario copia y pega; no reconstruye URLs |
| Marcá como "no instalada" lo que no instalaste | Si no, el usuario cree que `$bloc` ya está disponible |
| No prometas tiempos ni resultados | El reporte es un diagnóstico del proyecto, no una promesa de resultados |
| Si no pudiste determinar el stack | Decilo y preguntá; no completes con la opción más común |
