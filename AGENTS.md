---
name: ecosystem-bootstrap-agent-skill
description: Bootstraps the Agent-Rules-Ecosystem governance into a project by detecting the stack, recommending the right *-agent-rules core, installing it as a submodule in .agents/, scaffolding overview/, and listing (not installing) compatible skills.
---

# 🌐 Ecosystem Bootstrap Skill Directive

## Bootstrap de la Habilidad

1. `core/commands.md` ← **primero siempre**; define `$ecoproject` y `$ecoproject:audit` y delimita el alcance
2. `core/brain.md` ← matrices de decisión: qué detectar, qué desempate, cuándo no actuar
3. `core/path_map.md` ← rutas canónicas que la skill lee (`.agents/`, `.skill/`, `overview/`) y escribe
4. `knowledge/detection.md` ← señales por dominio, pesos y desempates
5. `knowledge/catalogo_gobernanzas.md` ← catálogo offline verificado (respaldo cuando no hay red)
6. `knowledge/instalacion_overview.md` ← procedimiento de install + scaffold + troubleshooting

## Reglas Canónicas [Transversales]

1. **Nunca escribas dentro de `.agents/` ni de `.skill/*/`.** Ambos son de solo lectura en el proyecto huésped; el único lugar editable es `overview/`.
2. **Nunca recomiendes un repositorio que no exista.** Verificá con `git ls-remote --exit-code -h` o `gh api` antes de ofrecerlo; el catálogo del ecosistema publica promesas que todavía no están en GitHub.
3. **Nunca instales gobernanza sin confirmación explícita del usuario.** La confirmación de la gobernanza no autoriza la instalación de skills.
4. **Nunca instales skills sin que el usuario las pida.** Se listan con su evidencia; si el usuario no dice nada, se ignoran.
5. **Nunca pises un archivo existente de `overview/`.** El scaffold es aditivo. Y no completes `overview/` con contenido propio: ni tareas, ni deuda, ni notas derivadas de leer el código.
6. **Nunca reimplementes ni ejecutes los rituales de gobernanza.** `$boot`, `$work`, `$close`, `$archi` y `$learn` pertenecen al core `*-agent-rules` instalado en `.agents/` y los dispara el usuario.
7. **Una sola gobernanza por proyecto.** `.agents/` no admite submódulos anidados; si el proyecto es multistack, la gobernanza principal define el marco y la alternativa se documenta.
8. **No commitees.** Mencioná que la instalación queda pendiente de commit; el commit es del usuario.
