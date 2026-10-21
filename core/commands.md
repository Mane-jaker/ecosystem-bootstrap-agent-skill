# Comandos de la Skill (`$-comandos`)

La skill se activa por nombre (`skill({name: "ecosystem-bootstrap-agent-skill"})`), por slash (`/ecosystem-bootstrap-agent-skill`) o por auto-invocación cuando la descripción aplica. Los comandos de abajo son el atajo compatible con el estándar del ecosistema y la forma de invocarla desde dentro de un proyecto que ya tiene gobernanza.

---

## `$ecoproject`

Bootstrap completo del proyecto: detección, recomendación, instalación y scaffold.

**Pasos del agente:**

1. Si `.agents/` existe → ejecutar `$ecoproject:audit` y devolver. No reinstalar.
2. Detectar el stack leyendo archivos reales de la raíz (ver `knowledge/detection.md`).
3. Resolver el catálogo en runtime desde `ESTRUCTURA_CLONACION.md` del repo núcleo y verificar existencia de cada repo.
4. Armar el informe (formato en `resources/formato_informe.md`) con evidencia, recomendación única y alternativas.
5. Preguntar **una vez**: ¿instalar la recomendada / una alternativa / no instalar?
6. Con confirmación: `git init` si hace falta → `git submodule add https://github.com/Agent-Rules-Ecosystem/<repo>.git .agents` → verificar con `git submodule status` y la presencia de `.agents/AGENTS.md`, `.agents/core/commands.md`, `.agents/templates/`.
7. Scaffoldear `overview/` desde `.agents/templates/` sin sobreescribir nada.
8. Listar las skills candidatas (mencionadas, no instaladas) con su evidencia y comando.
9. Reportar y cerrar con **"próximo paso: `$boot`"**.

**Confirmación esperada:**
`Gobernanza <repo> instalada en .agents/ · overview/ scaffoldeado · próximo paso: $boot`

---

## `$ecoproject:audit`

Auditoría de la instalación existente. **No modifica nada** salvo que falta `overview/`, en cuyo caso ofrece el scaffold.

**Pasos del agente:**

1. Verificar `.agents/` y sus archivos canónicos: `AGENTS.md`, `core/commands.md`, `core/brain.md`, `core/communication.md`, `core/path_map.md`, `templates/`, `knowledge/`.
2. Verificar `overview/` y su árbol mínimo: `session.md`, `work.md`, `work/tasks.md`, `work/pendientes.md`, `work/deuda_tecnica.md`, `work_review.md`, `trackers/progress.md`.
3. Verificar `.skill/` → listar skills instaladas y, si cada una tiene `core/commands.md`, confirmar que su alias está en `overview/commands_project.md`.
4. Verificar integridad de submódulos: `git submodule status` (los módulos con prefijo `-` o `+` están desincronizados).
5. Reportar en 5 líneas: core detectado, estado de `overview/`, skills instaladas, flags (módulos desincronizados, archivos canónicos faltantes, skills sin auditar), próximo paso.

---

## `$ecoproject:catalog`

Resuelve y lista el catálogo disponible. Solo lectura.

**Pasos del agente:**

1. Descargar `ESTRUCTURA_CLONACION.md` del repo núcleo y extraer los repos `*-agent-rules` y `*-agent-skill(s)`.
2. Verificar existencia de cada uno (`git ls-remote --exit-code -h` / `gh api`).
3. Mostrar en dos tablas: **gobernanzas** (repo, dominio, alias de dominio) y **skills** (repo, alias `$`, dominio).
4. Marcar como *no publicado* los que no existen, sin ofrecerlos como instalables.

---

## Reglas de parsing

- El prefijo `$` debe ser el primer carácter del mensaje o estar en línea propia.
- Los comandos son case-insensitive: `$EcoProject` = `$ecoproject`.
- Si falta un argumento requerido, pedilo en una sola línea.
- Comando desconocido: `Comando desconocido. Disponibles: $ecoproject $ecoproject:audit $ecoproject:catalog`.
