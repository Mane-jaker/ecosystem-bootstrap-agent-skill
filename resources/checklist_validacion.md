# Checklist de validación

Contra `SKILL_STANDARD.md` del `Agent-Rules-Ecosystem`.

---

## 📁 Estructura

- [ ] `README.md` con las 4 secciones requeridas (Descripción, Propósito y Alcance, Comandos, Arquitectura, Instalación)
- [ ] `AGENTS.md` con frontmatter YAML válido (`name` = nombre exacto del repo)
- [ ] `SKILL.md` con frontmatter (`name`, `description` de 1–1024 chars, `license`, `metadata`) + al menos un diagrama Mermaid
- [ ] `adapters/GEMINI.md` con trigger y rutas
- [ ] `adapters/CLAUDE.md` con trigger y rutas
- [ ] `adapters/AGENTS.md` con trigger y ruta
- [ ] `adapters/cursor-rule.mdc` con frontmatter `description` y `globs`
- [ ] `adapters/README.md` índice de adaptadores
- [ ] `core/commands.md` con la lista de `$-comandos`
- [ ] `core/brain.md` con matrices de decisión
- [ ] `core/path_map.md` con rutas canónicas
- [ ] `knowledge/` con mínimo 2 archivos (hay 4)
- [ ] `resources/` con al menos 1 template (hay 2)
- [ ] `scripts/` con scripts explicados en Markdown

---

## 📝 Contenido

- [ ] `name` del frontmatter coincide con el nombre del directorio
- [ ] Al menos 1 diagrama Mermaid (en `SKILL.md` o `README.md`)
- [ ] Los `$-comandos` de `core/commands.md` coinciden con los del `README.md`
- [ ] `core/path_map.md` usa rutas genéricas, no hardcodeadas a un proyecto
- [ ] Los adaptadores apuntan a rutas relativas dentro del repo
- [ ] Todo el contenido es 100% Markdown (nada de `.sh`, `.py`, `.json` ejecutables crudos)
- [ ] El nombre de los comandos `$` no colisiona con los del core: el ecosistema usa `$ecosystem` (auditoría global) y `$workeco`; esta skill usa `$ecoproject`

---

## 🌐 Calidad transversal (esta skill es transversal)

- [ ] No contiene referencias a un lenguaje o plataforma específica en las reglas canónicas
- [ ] Los comandos de ejemplo usan placeholders genéricos (`<repo>`, `<skill>`)
- [ ] `AGENTS.md` no menciona rutas propias de un framework concreto

---

## 🔗 Coherencia con el ecosistema

- [ ] Los nombres de repo citados corresponden a repos que **existen** (verificado con `git ls-remote` o `gh api`)
- [ ] Los repos citados como "no publicados" están marcados como tales
- [ ] La instalación usa URLs **HTTPS** (no SSH) para no depender de llaves
- [ ] `.agents/` se trata como de solo lectura en todo el documento
- [ ] Las skills se listan con evidencia y no se instalan sin pedido
- [ ] La skill delega explícitamente los rituales `$boot`/`$work`/`$close` a la gobernanza instalada

---

## 🧪 Comportamiento (verificar con los evals)

- [ ] No reinstala si `.agents/` ya existe
- [ ] No instala sin confirmación explícita
- [ ] No ofrece repos inexistentes
- [ ] No pisa `overview/` existente
- [ ] No instala skills sin pedido
- [ ] Reporta el stack con evidencia y hace una sola pregunta
