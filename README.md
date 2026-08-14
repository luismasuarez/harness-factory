# Harness Factory 🏭

> Genera harnesses de agentes a partir de intent en lenguaje natural, usando el
> ecosistema de agent skills como pool de talento experto.

**Harness Factory** es un estándar open source que convierte un intent
("refactorizar X a DDD", "crear un SaaS de billing", "auditar seguridad") en un
**harness completo**: un orquestador + expertos (skills) + ejecutor, con un
runtime de seguridad fijo (gates, aprobación, slices, tripwire).

## Qué resuelve

Los coding agents ya tienen skills increíbles en el ecosistema
(`npx skills`, skills.sh, agent skills de vercel/anthropic/prisma/...), pero no
existe una forma estándar de:

- **Seleccionar** expertos según la tarea (qué skills contratar).
- **Definir** quién ejecuta (write-scoped) y quién solo analiza (read-only).
- **Coordinar** el equipo con un orquestador + pipeline.
- **Garantizar seguridad**: baseline gate, aprobación explícita, slices
  behavior-preserving, tripwire que revierte en rojo.

Ese es el gap: **la meta-capa que arma harnesses por tarea desde el catálogo de
skills**, como estándar instalable. Superpowers es una metodología fija de SDLC;
Harness Factory es el *factory* que genera harnesses acotados por tarea.

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
├── examples/ddd-architecture/← dogfooding: manifest + artefactos reales
├── .opencode/INSTALL.md      ← instalación en opencode
├── SECURITY.md               ← política de seguridad y reglas para contribuidores
└── docs/                     ← guías y referencia
```

## Roadmap

- [x] Fase 1 — Scaffold del repo
- [x] Fase 2 — Core del estándar (schema, templates, runtime, factory, catalog)
- [x] Fase 3 — Distribución e instalación (`npx skills add`, `/factory`)
- [x] Fase 4 — Dogfooding (`examples/ddd-architecture`)
- [x] Fase 5 — Docs + cierre

## Estado

Las 5 fases del MVP están completadas y el repo está publicado
(`luismasuarez/harness-factory`). Pendiente:
- Verificación real del dogfooding: correr `factory build` contra el harness DDD de `portal_saas` y hacer el diff de equivalencia.
- Publicación en skills.sh (`npx skills publish`).

## Disclaimer

> **Estándar experimental.** Harness Factory es un MVP que estandariza un flujo
> validado en un solo proyecto (portal_saas). Úsalo a tu propio riesgo: el
> estándar (schema/templates/runtime) puede evolucionar con breaking changes
> hasta 1.0. Los harnesses generados ejecutan cambios sobre tu código — revisá
> siempre el roster propuesto y el plan antes de aprobar la ejecución.
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
