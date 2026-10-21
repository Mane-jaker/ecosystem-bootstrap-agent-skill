# Mapa canónico de rutas

## Lectura (read-only)

| Recurso | Ruta | Carga |
|---|---|---|
| Directiva de arranque de la gobernanza | `.agents/AGENTS.md` | Obligatoria si `.agents/` existe |
| Comandos de la gobernanza | `.agents/core/commands.md` | Obligatoria si `.agents/` existe |
| Brain de la gobernanza | `.agents/core/brain.md` | Bajo demanda |
| Comunicación / modo lacónico | `.agents/core/communication.md` | Bajo demanda |
| Mapa de rutas de la gobernanza | `.agents/core/path_map.md` | Bajo demanda |
| Conocimiento del framework | `.agents/knowledge/` | Bajo demanda |
| Plantillas maestras | `.agents/templates/` | Obligatoria en el scaffold |
| Catálogo de skills del core | `.agents/skills/README.md` | Obligatoria en el Paso 6 |
| Skills inline del core | `.agents/skills/<skill>/SKILL.md` | Bajo demanda — ya vienen instaladas |
| Skills instaladas | `.skill/<repo-completo>/` | Bajo demanda |
| Comandos de skill instalada | `.skill/<repo-completo>/core/commands.md` | Bajo demanda |
| Reportes activos de skills | `overview/work/skill/<skill>.md` | Bajo demanda |

## Escritura

| Ruta | Qué se escribe | Quién escribe |
|---|---|---|
| `.agents/` | Nada. Submódulo de solo lectura | El core (no el agente) |
| `.skill/<repo>/` | Nada. Submódulos de solo lectura | El core (no el agente) |
| `overview/` | Todo el estado del proyecto | Esta skill (solo scaffold) + rituales `$boot`/`$work`/`$close` |
| `overview/session.md` | `Agente:`, `## Cambios`, `## Reanudar` | `$boot` / `$close` |
| `overview/work.md` | Índice maestro de tareas | `$work` / `$close` |
| `overview/work/tasks.md` | Tarea activa e historial de intentos | `$work` |
| `overview/work/pendientes.md` | Seguimiento de baja prioridad | `$work` / `$close` |
| `overview/work/deuda_tecnica.md` | Deuda técnica por prioridad Alta/Media/Baja | `$work` / `$close` |
| `overview/work_review.md` | Motor de priorización | `$boot` |
| `overview/trackers/progress.md` | Nodo activo y progreso | `$close` |
| `overview/architecture.md` + `overview/architecture/` | Arquitectura viva Hub & Spoke | `$archi` |
| `overview/learning.md` | Propuestas de mejora | `$learn` / `$learnagnostico` |
| `overview/commands_project.md` | Registro vivo de `$`-comandos | `$boot` |

## Remotos (fuera del proyecto)

| Recurso | URL |
|---|---|
| Catálogo canónico del ecosistema | `https://raw.githubusercontent.com/Agent-Rules-Ecosystem/agent-rules-ecosystem/main/ESTRUCTURA_CLONACION.md` |
| Manifiesto maestro | `https://raw.githubusercontent.com/Agent-Rules-Ecosystem/agent-rules-ecosystem/main/README.md` |
| Estándar de rules | `https://raw.githubusercontent.com/Agent-Rules-Ecosystem/agent-rules-ecosystem/main/RULES_STANDARD.md` |
| Estándar de skills | `https://raw.githubusercontent.com/Agent-Rules-Ecosystem/agent-rules-ecosystem/main/SKILL_STANDARD.md` |
| Base de repos oficiales | `https://github.com/Agent-Rules-Ecosystem/<repo>.git` |

## Verificación de existencia de un repo

```bash
git ls-remote --exit-code -h https://github.com/Agent-Rules-Ecosystem/<repo>.git >/dev/null 2>&1 && echo EXISTS || echo MISSING
# alternativa
gh api repos/Agent-Rules-Ecosystem/<repo> --jq .name
```

## Scaffold de `overview/`

Origen: `.agents/templates/`. Destino: `overview/` en la raíz del proyecto. Copia aditiva (`cp -n` / `cp -rn`), nunca sobreescribe. Se omiten los placeholders de fecha (`sesion_YYYY-MM-DD.md`): los crea el ritual `$boot` con la fecha real.
