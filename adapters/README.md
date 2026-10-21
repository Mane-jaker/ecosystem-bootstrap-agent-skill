# Adapters

Adaptadores por herramienta de IA. Permiten que cada agente lea la skill en su formato nativo.

| Archivo | Herramienta | Trigger |
|---|---|---|
| `AGENTS.md` | OpenAI / Codex / universal | Carga esta skill al arrancar un proyecto o al mencionar gobernanza / agent rules |
| `CLAUDE.md` | Anthropic Claude | Skill `ecosystem-bootstrap-agent-skill` disponible para bootstrap de gobernanza |
| `GEMINI.md` | Google Gemini / Antigravity | Skill `ecosystem-bootstrap-agent-skill` disponible para bootstrap de gobernanza |
| `cursor-rule.mdc` | Cursor IDE | Regla de bootstrap del ecosistema |

Todos apuntan a la misma raíz: `SKILL.md` (cuerpo), `AGENTS.md` (directiva de arranque), `core/commands.md` (comandos `$ecoproject`).
