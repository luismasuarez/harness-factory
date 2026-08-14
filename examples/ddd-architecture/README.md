# Example: ddd-architecture

The **living README / acceptance proof** of the Harness Factory standard.

This example is a reverse-engineering of the real DDD Mixture-of-Experts harness
built and battle-tested in a private TypeScript monorepo (bounded context
`licenses`, 10 slices, 19 commits, gates per slice with tripwire).

## What it proves

| Claim | How it's proven here |
|---|---|
| A real harness is fully described by a manifest | `harness.manifest.json` captures the entire roster, scopes, gates and pipeline |
| The runtime is fixed | Every invariant (baseline, approval, slices, tripwire) lives in `skills/harness-runtime/SKILL.md`, not in this manifest |
| Reproducible (`factory build`) | Regenerating from this manifest must produce the identical harness in the target repo — diff-of-equivalence is the acceptance test |
| Catalog → experts | Each `expert-*` in the manifest maps to a descriptor in `catalog/seed-ddd-architecture.json` |

## The roster

Six analysis experts (read-only) + one executor (write-scoped) + one orchestrator
(primary), matching the skills ecosystem:

| Expert | Skill | Deliverable |
|---|---|---|
| `expert-strategic` | `ddd-strategic-design` | `context-map.md` |
| `expert-tactical` | `domain-driven-design` | `domain-score.md` |
| `expert-clean` | `clean-architecture` | `dependency-plan.md` |
| `expert-events` | `event-sourcing-architect` | `event-design.md` |
| `expert-nestjs` | `nestjs-best-practices` | `nestjs-plan.md` |
| `expert-prisma` | `prisma-client-api` + `prisma-database-setup` | `persistence-plan.md` |

Handoff chain (Task DAG): each expert's `input` references the previous experts'
deliverables; order is derived from the `pipeline`, not hardcoded in prompts.

## How to regenerate the reference harness

In a checkout of the target repo (same stack as the reference):

```bash
/factory build           # reads harness.manifest.json from examples/ddd-architecture/
```

Acceptance: diff the regenerated harness against the existing one — must be empty.
