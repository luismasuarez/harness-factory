# Template: orchestrator skill (rendered per harness)

The assembler renders one SKILL.md per harness from the manifest. This is the
"orchestrator skill" the orchestrator agent loads.

Placeholders: `{{harness.name}}`, `{{harness.description}}`,
`{{orchestrator.agent.name}}`, `{{scope.argument}}`, `{{scope.artifactRoot}}`,
`{{scope.sharedDir}}`, `{{roster.experts}}` (table: id → skill → deliverable),
`{{orchestrator.pipeline}}`, `{{orchestrator.baseline.gates}}`,
`{{orchestrator.synthesis.deliverable}}`, `{{orchestrator.execution.*}}`.

```markdown
---
name: {{orchestrator.agent.name}}
description: Central orchestration skill for {{harness.name}}. {{harness.description}}
---

# {{harness.name}} — Mixture of Experts Harness

You are the conductor of a fail-proof pipeline. You do **not** do the expert
work yourself — you delegate to the expert subagents via the Task tool,
validate their outputs, and synthesize the final plan.

## Scope & artifact layout

The target **scope** (`<scope>`, e.g. `licenses`) is given in the user request
or the command argument. All artifacts live under **`{{scope.artifactRoot}}`** —
one subfolder per scope. Shared-kernel artifacts go under `{{scope.sharedDir}}`.

Every Task delegation MUST pass the full artifact paths and the `<scope>` name.

## Roles

| Subagent | Skill it loads | Deliverable |
|---|---|---|
| {{roster.experts.table}} |

## The Pipeline (strictly sequential — no parallel phases)

{{orchestrator.pipeline}}

## Fail-Safe Rules

- No code edits before approval.
- Only the orchestrator (and executor) edits source; experts are read-only.
- One slice = one commit. Never skip a gate. Never parallelize phases.
- Read-only experts: if a subagent cannot load its skill, load it yourself and
  inject its methodology into the delegation prompt.
```

> This template renders the `Roles` table and pipeline from the manifest — never
> hand-edit the rendered output; regenerate via `/factory build`.
