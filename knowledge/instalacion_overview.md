# Instalación y scaffold

Procedimiento detallado del paso 4 y 5 de la skill.

---

## 1. Requisitos previos

| Requisito | Verificación | Si falla |
|---|---|---|
| `git` disponible | `git --version` | No se puede instalar nada: reportá y dá la instrucción manual |
| Acceso de lectura al repo | `git ls-remote --exit-code -h <url>` | Sin red → usar el catálogo offline; sin permisos → credenciales |
| Proyecto es repo git | `git rev-parse --is-inside-work-tree` | `git init` en la raíz (requiere confirmación del usuario) |
| `.agents/` no existe | `ls -d .agents` | Si existe: no es una instalación nueva → short-circuit |

---

## 2. Instalación de la gobernanza

```bash
# 2.1 Asegurar repo git
git rev-parse --is-inside-work-tree >/dev/null 2>&1 || git init

# 2.2 Instalar el core como submódulo
git submodule add https://github.com/Agent-Rules-Ecosystem/<repo>.git .agents

# 2.3 Verificar
git submodule status
ls .agents/AGENTS.md .agents/core/commands.md .agents/core/brain.md .agents/templates/
```

### Por qué HTTPS y no SSH

El `README.md` del ecosistema usa `git@github.com:Agent-Rules-Ecosystem/...`, que exige llaves SSH en la máquina. HTTPS funciona con cualquier cliente autenticado (credential helper, `gh auth setup-git`, token). La URL HTTPS es la misma repo, solo cambia el transporte.

### Por qué submódulo y no clone

El estándar del ecosistema define `.agents/` como submódulo de solo lectura. Un `git clone` directo:
- no registra el origen en `.gitmodules` → se pierde la trazabilidad de qué gobernanza está instalada,
- complica la actualización (`git submodule update --remote`),
- rompe la regla "cambios a reglas globales solo en el repo oficial".

### Actualización posterior

```bash
git submodule update --remote .agents
git submodule status
```

---

## 3. Scaffold de `overview/`

```bash
mkdir -p overview
cp -rn .agents/templates/. overview/ 2>/dev/null || true
rm -f overview/history/sesion_YYYY-MM-DD.md
```

### Reglas del scaffold

| Regla | Por qué |
|---|---|
| Aditivo: nunca sobreescribir | `overview/` contiene el trabajo en curso; pisarlo perdería el track |
| Origen único: `.agents/templates/` | Las plantillas son la especificación; inventar contenido genera drift |
| Omitir placeholders de fecha (`*_YYYY-MM-DD.md`) | Son plantillas de ejemplo, no estado real; `$boot` crea el archivo con la fecha correcta |
| No completar `session.md` con contenido inventado | `Agente:` y `## Reanudar` los escribe `$boot` / `$close` |

### Árbol esperado tras el scaffold

```
overview/
├── session.md            ← estado y handoff
├── work.md               ← índice maestro
├── work_review.md        ← motor de priorización
├── commands_project.md   ← registro vivo de comandos
├── learning.md           ← buffer de lecciones
├── architecture.md       ← índice Hub
├── architecture/         ← spokes (routes_map, core/, modules/)
├── work/
│   ├── tasks.md
│   ├── pendientes.md
│   └── deuda_tecnica.md
├── trackers/
│   ├── progress.md
│   ├── architecture.md
│   └── content_*.md
├── context/
├── workflows/
└── history/
```

> La forma exacta depende de la versión de las plantillas del core instalado. `cp -rn` copia lo que exista; `$boot` completa lo que falte en la siguiente iteración.

---

## 4. Instalación de una skill (solo si el usuario la pide)

```bash
mkdir -p .skill
git submodule add https://github.com/Agent-Rules-Ecosystem/<skill-repo>.git .skill/<skill-repo>
```

- El nombre del directorio es el **nombre completo del repo** (`.skill/web-react-agent-skill/`), nunca un alias corto.
- `.skill/` vive en la raíz del proyecto, **fuera** de `.agents/`. Un submódulo anidado dentro de otro submódulo es un error de Git.
- Verificación: `ls .skill/<skill-repo>/SKILL.md .skill/<skill-repo>/core/commands.md`

---

## 5. Troubleshooting

| Síntoma | Causa | Solución |
|---|---|---|
| `fatal: not a git repository` | Sin repo git | `git init` + reintentar |
| `destination path '.agents' already exists and is not an empty directory` | Ya hay contenido en `.agents/` | **No borrar.** Reportar y preguntar: ¿es una instalación manual previa? ¿se reemplaza? |
| `Permission denied (publickey)` | URL SSH | Cambiar a `https://github.com/Agent-Rules-Ecosystem/<repo>.git` |
| `could not read Username for 'https://github.com'` | Credenciales HTTPS | `gh auth setup-git` → reintentar una vez → si falla, dar el comando manual al usuario |
| `fatal: remote error: upload-pack: not our ref` | Ref inexistente | `git submodule sync` + `git submodule update --init --recursive` |
| `git submodule status` muestra prefijo `-` | Módulo no inicializado | `git submodule update --init --recursive` |
| `fatal: repository not found` en `ls-remote` | Repo no publicado | **No es instalable.** Volver al catálogo runtime y marcarlo como no publicado |
| El scaffold no copió nada | `cp` no disponible o rutas distintas | Verificá `ls .agents/templates/` y reportá; el `$boot` de la gobernanza completa el scaffold igual |

---

## 6. Verificación posterior a la instalación

```bash
git submodule status                                   # 1. módulo registrado y sincronizado
ls .agents/AGENTS.md .agents/core/ .agents/templates/ # 2. estructura canónica
ls overview/                                           # 3. track vivo creado
git status                                             # 4. qué quedó pendiente de commitear
```

El último punto importa: `git submodule add` stagea `.gitmodules` y el contenido del módulo, pero el `.gitmodules` **queda sin commitear** hasta que el usuario haga `git commit`. Mencioná eso en el reporte: la instalación no está completa hasta que se commitea.
