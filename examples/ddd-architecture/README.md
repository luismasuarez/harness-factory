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

In a checkout of the target project (portal_saas-like repo):

```bash
/factory build           # reads harness.manifest.json from examples/ddd-architecture/
```

Acceptance: diff the regenerated harness against the existing one — must be empty.

## Dogfooding result (equivalence verified)

`factory build` was simulated from this manifest + `templates/` and diffed
against the real harness extracted from the target project:

- **Agents** (6 experts + executor + orchestrator): descriptions verbatim,
  permissions verbatim (experts `docs/architecture/**`, prisma dual-skill,
  executor write-scoped), prompts structurally equivalent. ✅
- **Command** `/ddd-plan`: identical (description/agent/template). ✅
- **AGENTS golden-rule block**: same trigger + 5 invariants (baseline, read-only,
  approval, slices, per-slice gates) + Commands. ✅
- **Orchestrator skill**: Phase 0 baseline (git-clean + record SHA + 4 gates +
  stop-on-red), handoff rule, between-phase validation, slice rule, Artifacts
  section. ✅

Structural fixes the dogfooding drove into the standard:
1. `expert.skills[]` (array) instead of a single `skill` — enables multi-skill
   experts (prisma loads `prisma-client-api` + `prisma-database-setup`).
2. Orchestrator templates render the manifest's baseline gate, HANDOFF RULE,
   slice rule and shared dir (they were declared but unrendered).
3. Expert edit allowlist widened to the artifact base tree
   (`scope.artifactBase`, e.g. `docs/architecture/**`) so experts can also write
   shared-kernel artifacts.
4. Per-expert `description` / `task` / `summary` and executor
   `hardConstraints` move the polished prose into the manifest (source of truth).
5. `command.name` has NO leading slash (opencode convention); display renders
   `/name`.
6. Booleans (`cleanWorkingTree`, `recordSha`, `approvalRequired`) render as words,
   not literal `true`/`false`.

Remaining diffs are cosmetic: language (ES/EN), `<scope>` vs `<bounded-context>`
token naming, prose polish — no structural gaps.
