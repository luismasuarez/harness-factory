# Harness Factory — Repo Rules

## What this repo is

An open-source standard that **generates agent harnesses** from natural-language intent,
using the ecosystem of agent skills as the "expert talent pool".

It is tooling/config work — a factory that produces harnesses for *other* projects.
It does NOT perform domain work on any target project itself; it scaffolds the harness,
and the generated harness (in the target repo) does the domain work.

## Golden rule: the harness-factory skill is the entry point

Any task that involves **creating, regenerating, or validating a harness** MUST go
through the `harness-factory` skill (and the `harness-runtime` invariants it loads).

Never hand-write agents/skills/commands for a harness ad hoc — use `/factory` or load
the skill and follow its pipeline (intent → roster → confirm → manifest → assemble).

## Invariants of the standard

- **Manifest is the source of truth.** A harness is fully described by its
  `harness.manifest.json`, validated against `schema/harness.manifest.schema.json`.
  Nothing is generated from ad-hoc state; everything derives from the manifest.
- **Factory proposes, user disposes.** The factory proposes a roster (experts +
  executor + orchestrator) and waits for explicit confirmation before freezing the
  manifest. "Carrito de compras" gate.
- **Missing skills pause, never silently degrade.** If the intent requires a skill that
  isn't installed, the factory PAUSES and asks the user to `npx skills add ...`.
- **Fixed runtime, variable team.** The security model (read-only experts, scoped
  executor, baseline gate, approval gate, slices, tripwire) is FIXED and never changes
  per harness. Only the roster/pipeline/scopes are variable (declared in the manifest).
- **Reproducible.** A manifest regenerates the identical harness (`build` mode). Diff of
  equivalence is the acceptance test.
- **AGENTS.md format.** Golden-rule blocks emitted into target projects follow the
  `agents.md` open format (Agentic AI Foundation).

## Editing conventions

- Never edit `templates/` role templates ad hoc without updating the schema and the
  `harness-runtime` skill in the same change (they must stay in sync).
- `catalog/` role descriptors are declarative data; the selector logic lives in the
  `harness-factory` skill, not in the catalog files.
- When the standard changes, update `examples/ddd-architecture/` to match (it is the
  living README / acceptance proof).
