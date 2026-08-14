# Templates

Fixed, standard templates used by the `harness-factory` skill to assemble a
harness from a manifest. **These are the "fixed" part of the standard** — the
security model (read-only experts, scoped executor, orchestrator) never changes
per harness. Only the roster/pipeline/scopes are variable (declared in the
manifest).

## Layout

```
templates/
├── roles/        ← the three role templates (analysis-expert, executor, orchestrator)
├── pieces/       ← smaller rendered pieces (command, orchestrator skill)
└── blocks/       ← standalone blocks (AGENTS.md golden-rule)
```

## Invariants

- **Never edit these ad hoc.** Any change must update, in the same change:
  1. the affected role template here,
  2. `schema/harness.manifest.schema.json` (if the placeholder/source changed),
  3. `skills/harness-runtime/SKILL.md` (the invariants they enforce).
- Placeholders use `{{token}}` syntax; their sources are documented in each
  template's placeholder table.
- The rendered output is generated, never hand-maintained: regenerate via
  `/factory build`.
