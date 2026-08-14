# Harness Factory 🏭

![License](https://img.shields.io/github/license/luismasuarez/harness-factory)
![Version](https://img.shields.io/github/v/release/luismasuarez/harness-factory)
![Stars](https://img.shields.io/github/stars/luismasuarez/harness-factory)
![Last commit](https://img.shields.io/github/last-commit/luismasuarez/harness-factory)
![Repo size](https://img.shields.io/github/repo-size/luismasuarez/harness-factory)
![Made for opencode](https://img.shields.io/badge/Made%20for-opencode-2671E8)

> Estandariza el flujo de trabajo de los coding agents: un intent en lenguaje
> natural → un harness versionado (orquestador + expertos + ejecutor) con un
> runtime de seguridad y verificación continua.

**Harness Factory** es una propuesta de arquitectura estándar para componer el
ecosistema de agent skills existente en **harnesses de trabajo reproducibles**:
un intent ("refactorizar X a DDD", "crear un SaaS de billing", "auditar
seguridad") se convierte en un equipo de agentes acotado a la tarea, con modelo
de permisos, gates de calidad y trazabilidad.

## Motivación

El ecosistema de skills ya resolvió el *qué saber*: `npx skills`, skills.sh y
los repos oficiales (vercel, anthropic, openai, prisma, microsoft…) proveen
conocimiento experto instalable para cualquier stack y tarea.

El gap está en el *cómo componer*. No existe un estándar para:

- **Seleccionar** expertos según la tarea (qué skills contratar) de forma
  reproducible y auditada.
- **Definir** quién ejecuta (write-scoped) y quién solo analiza (read-only) por
  construcción, no por buena voluntad.
- **Coordinar** un pipeline con gates de calidad y aprobación humana.
- **Versionar** el equipo y el proceso como un contrato, no como una conversación
  ad-hoc.

Consecuencia hoy: flujos de trabajo manuales, no versionados, sin gates ni
trazabilidad; el conocimiento experto disponible queda infrautilizado.

## Propuesta

El estándar se organiza en **cuatro planos**:

| Plano | Artefacto | Rol |
|---|---|---|
| **Contrato** | `harness.manifest.json` + JSON Schema | Declaración versionable del harness: equipo, pipeline, gates, scope |
| **Runtime** | `skills/harness-runtime` | Invariantes de ingeniería: seguridad por roles, baseline gate, aprobación, slices, tripwire |
| **Conocimiento** | `catalog/` + ecosistema skills.sh | Pool de expertos seleccionable por tarea |
| **Composición** | `skills/harness-factory` | Componedor: intent → roster → manifest → harness → ejecución |

## Garantías del estándar

- **Reproducibilidad** — mismo manifest → mismo harness. `factory build`
  regenera desde el manifest y la aceptación es un diff vacío.
- **Auditabilidad** — cada decisión y artefacto queda versionado en
  `docs/architecture/<scope>/`; el historial del harness es trazable commit a commit.
- **Seguridad por construcción** — expertos read-only (solo escriben en la
  carpeta de artefactos), executor write-scoped (`writePaths`), orquestador con
  `task` restringido al roster; aprobación humana antes de tocar código.
- **Verificación continua** — baseline gate con tripwire antes de comenzar y
  gates por slice tras cada cambio; en rojo, revert inmediato.
- **Agnóstico** — sirve para refactor, greenfield y auditoría; admite cualquier
  skill del ecosistema y cualquier stack.

## Cómo funciona

```
[1] Intento      → "Quiero un SaaS de billing con pagos y auditoría"
[2] Descubrir    → catálogo local (skills instalados) + skills.sh (opcional)
[3] Proponer     → roster de expertos + executor + pipeline → VOS CONFIRMAS
[4] Congelar     → harness.manifest.json (reproducible, editable, versionable)
[5] Ensamblar    → orquestador + agentes + comando + golden-rule
[6] Ejecutar     → baseline gate → análisis → síntesis → aprobación → slices
```

## Instalación

```bash
npx skills add <owner>/harness-factory -g
```

Luego, en cualquier repo:

```bash
/factory "refactorizar X hacia DDD"
# o, si ya hay manifest:
/factory build
```

## Estructura del estándar

```
harness-factory/
├── skills/harness-factory/   ← el factory (intent → roster → manifest → genera)
├── skills/harness-runtime/   ← el runtime fijo (gates, aprobación, slices, memoria)
├── templates/                ← plantillas de roles y piezas del harness
├── schema/                   ← harness.manifest.schema.json (el contrato)
├── catalog/                  ← descriptores de rol (skills → candidatos a experto)
├── examples/ddd-architecture/← caso de referencia validado en producción
├── .opencode/INSTALL.md      ← instalación en opencode
├── SECURITY.md               ← política de seguridad y reglas para contribuidores
└── docs/                     ← guías y referencia
```

## Comparación

### vs. generadores de producto (Lovable / v0)

Los generadores de producto entregan un **artefacto** a partir de un prompt.
Harness Factory entrega un **proceso versionado**: no compite con ellos en
velocidad de prototipado, sino que aporta lo que les falta — trazabilidad,
control y mantenibilidad.

| Propiedad | Lovable / v0 | Harness Factory |
|---|---|---|
| Unidad de salida | Artefacto generado (app/mockup) | Proceso versionado (harness) |
| Procedencia | Caja negra — sin trazabilidad | Cada decisión auditable y versionada |
| Control de calidad | Sin gates | Baseline + por slice + tripwire |
| Contexto de ejecución | Sandbox del producto | Tu repo, tu stack, tus comandos |
| Ciclo de vida | Prototipo rápido | Producto/refactor mantenible |
| Complementariedad | Validación de idea | Construcción seria (o reconstrucción del prototipo) |

### vs. frameworks de metodología (Superpowers)

Superpowers es una **metodología fija** de SDLC empaquetada como skills
componibles. Harness Factory es una **meta-capa** que genera el harness acotado
a cada tarea, seleccionando expertos del catálogo en lugar de imponer un
workflow.

| Propiedad | Superpowers | Harness Factory |
|---|---|---|
| Tipo | Metodología fija de SDLC | Meta-capa que genera harnesses por tarea |
| Selección de expertos | Workflow fijo | Por tarea, desde el catálogo de skills |
| Contrato | Skills | Manifest + JSON Schema versionado |
| Reproducibilidad | Workflow fijo | `build` + diff vacío |
| Permisos | Delegación de subagentes | Read-only expertos / write-scoped executor |
| Alcance | Cómo construir software | Arma el equipo para cualquier tarea |

## Validación

El estándar se deriva de una refactorización real hacia DDD de un módulo crítico
de un monorepo TypeScript (pnpm workspace):

- **10 slices** behavior-preserving, uno por bounded context, cada uno con sus
  gates verdes y su commit.
- **Red de caracterización previa** (tests que fijan el comportamiento actual)
  como red de seguridad de los slices posteriores.
- **Sin regresiones** — la suite final supera la inicial y todos los gates
  (typecheck, lint sin errores nuevos, test, build) verdes tras cada slice.
- El manifest de esa refactorización vive en
  [`examples/ddd-architecture/`](./examples/ddd-architecture/) como caso de
  referencia y prueba de aceptación del estándar.

## Roadmap

- [x] Fase 1 — Scaffold del repo
- [x] Fase 2 — Core del estándar (schema, templates, runtime, factory, catalog)
- [x] Fase 3 — Distribución e instalación (`npx skills add`, `/factory`)
- [x] Fase 4 — Dogfooding (`examples/ddd-architecture`)
- [x] Fase 5 — Docs + cierre

## Estado

Las 5 fases del MVP están completadas y el repo está publicado
(`luismasuarez/harness-factory`). Pendiente:

- Ejecutar la verificación formal del dogfooding (`factory build` + diff de
  equivalencia) documentada en `examples/ddd-architecture/`.
- Publicación en skills.sh (`npx skills publish`).

## Disclaimer

> **Estándar experimental.** Harness Factory es un MVP que estandariza un flujo
> validado en un proyecto de referencia. Úsalo a tu propio riesgo: el estándar
> (schema/templates/runtime) puede evolucionar con breaking changes hasta 1.0.
> Los harnesses generados ejecutan cambios sobre tu código — revisá siempre el
> roster propuesto y el plan antes de aprobar la ejecución.
>
> **Seguridad.** Este estándar vive dentro de coding agents y puede tocar
> credenciales de tu entorno. Lee [SECURITY.md](./SECURITY.md): nunca commitees
> transcripts de sesiones ni secrets en repos generados con esta herramienta.

## Documentación

- `docs/guide-3-steps.md` — instalar, crear un harness, regenerar.
- `docs/roles.md` — los 3 roles fijos y el runtime.
- `docs/schema-reference.md` — el manifest explicado.

## Licencia

[MIT](./LICENSE)
