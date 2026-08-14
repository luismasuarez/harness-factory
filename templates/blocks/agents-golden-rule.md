# Template: AGENTS.md golden-rule block

Emitted into the target project's `AGENTS.md` by the assembler. Follows the
`agents.md` open format (Agentic AI Foundation, Linux Foundation).

Placeholders: `{{harness.name}}`, `{{command.name}}`, `{{orchestrator.agent.name}}`,
`{{roster.experts}}` (id list), `{{scope.artifactRoot}}`, `{{scope.sharedDir}}`,
`{{harness.description}}`, `{{synthesis.deliverable}}`, `{{execution.sliceRule}}`,
`{{executor.id}}`.

```markdown
## Golden Rule: <domain work> goes through the {{harness.name}} harness

Any task involving **{{harness.description}}** MUST go through the
{{harness.name}} Mixture-of-Experts harness.

**Trigger**: run `/{{command.name}} <scope>` or load the
`{{orchestrator.agent.name}}` skill. The orchestrator delegates to the expert
subagents (`{{roster.experts}}`) and synthesizes the plan.

**Never bypass the harness** by doing this work ad hoc.

## Harness invariants (enforced by `{{orchestrator.agent.name}}`)

- **Baseline gate**: typecheck + lint + test + build green before touching
  anything. If the baseline fails, no work starts.
- **Read-only experts**: experts ONLY analyze and write artifacts under
  `{{scope.artifactRoot}}` (one subfolder per scope;
  `{{scope.sharedDir}}/` for cross-scope contracts). They never edit source.
- **Approval**: the `{{synthesis.deliverable}}` (in the scope subfolder) is
  presented and waits for explicit OK before any edit.
- **Slices**: {{execution.sliceRule}}.
- **Gates per slice**: after each slice, typecheck + lint + test + build; if
  anything is red, revert the slice.

## Commands

- `/{{command.name}} <scope>` — runs the full harness pipeline (baseline →
  synthesis → approval → execution by slices).
```

> The target project's `AGENTS.md` may add its own sections (project title,
> global constraints); this block is the harness contract and must not be edited
> manually — regenerate via `/factory build` instead.
