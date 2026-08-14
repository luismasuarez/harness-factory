# Referencia del manifest

El `harness.manifest.json` es la **fuente de verdad** de un harness. El schema
canónico es `schema/harness.manifest.schema.json` (JSON Schema draft-07); esto
es un resumen legible.

## Estructura

```
harness.manifest.json
├── harness            → name, version, description, skillDescription, standardVersion
├── command            → name (ddd-plan, sin slash), description, agent, template
├── scope              → argument (scope), artifactRoot (docs/architecture/<scope>),
│                         artifactBase (docs/architecture), sharedDir
├── roster
│   ├── experts[]      → id, role, roleLabel, description, skills[], skillSource,
│   │                     deliverable, input[], readPaths[], trigger[], task, summary,
│   │                     overrides
│   └── executor       → id, description, hardConstraints[], writePaths[], readPaths[],
│                         bash[], gates, commit
└── orchestrator
    ├── agent          → name, description
    ├── baseline       → gates (commands), cleanWorkingTree, recordSha
    ├── pipeline[]     → fases: mkdir | delegate | synthesize | execute (+ description)
    ├── synthesis      → deliverable, approvalRequired
    └── execution      → mode (slices|single), sliceRule, perSliceGates
```

## Lo variable vs lo fijo

| Variable (en el manifest) | Fijo (en el runtime) |
|---|---|
| Roster: quién, con qué skill, qué entrega | Modelo de seguridad (read-only experts / write-scoped executor) |
| Scopes, artifactRoot, sharedDir | Baseline gate + tripwire |
| Pipeline (orden de delegación) | Gate de aprobación explícita |
| Gates concretos (comandos) | Slices behavior-preserving + characterization |
| Comando / nombre del harness | Denials verbatim, nunca trabajar por afuera |

## Convenciones

- `<scope>` en los paths es un placeholder reemplazado en runtime por el
  argumento del comando (ej. `docs/architecture/licenses/`).
- Los expertos referencian artefactos previos vía `input` (paths o `ref` a
  `expert.<id>.deliverable`); el orden del `pipeline` determina la secuencia
  (topological, no hardcodeada en los prompts).
- Un manifest se valida con `npx ajv-cli validate -s schema/harness.manifest.schema.json -d <manifest>`.
