# Role template: analysis-expert

This is a **fixed** role template of the Harness Factory standard. Do not edit ad hoc —
changes must stay in sync with `schema/harness.manifest.schema.json` and
`skills/harness-runtime/SKILL.md`.

The assembler renders this template once per manifest expert, substituting the
placeholders below.

## Placeholders

| Placeholder | Source |
|---|---|
| `{{expert.id}}` | `roster.experts[].id` |
| `{{expert.roleLabel}}` | `roster.experts[].roleLabel` |
| `{{expert.description}}` | `roster.experts[].description` (optional; fallback: `DDD <roleLabel> expert. Loads the <skills> skill(s).`) |
| `{{expert.skills}}` | `roster.experts[].skills` (array — one load per skill) |
| `{{expert.skillCall}}` | Rendered skill tool call(s). One skill: `skill({ name: "<skill>" })`. Two+: `skill({ name: "a" }) and skill({ name: "b" })`. |
| `{{expert.deliverable}}` | `roster.experts[].deliverable` |
| `{{expert.input}}` | `roster.experts[].input` (paths, refs resolved) |
| `{{expert.task}}` | `roster.experts[].task` (the domain task sentence) |
| `{{expert.summary}}` | `roster.experts[].summary` (what to return to the orchestrator) |
| `{{expert.overrides.prompt}}` | `roster.experts[].overrides.prompt` (optional extra constraints) |
| `{{scope.argument}}` | `scope.argument` |
| `{{scope.artifactRoot}}` | `scope.artifactRoot` |
| `{{scope.artifactBase}}` | `scope.artifactBase` (expert write allowlist root) |

## Rendered prompt (opencode agent `{{expert.id}}`)

```text
You are the {{expert.roleLabel}} expert in a DDD re-architecture pipeline.

MANDATORY FIRST STEP: load your skill by calling the skill tool: {{expert.skillCall}}. Read any reference files it provides.

TARGET DIR: the bounded-context name and artifact path are given in your task prompt (e.g. {{scope.artifactRoot}}/). Write ONLY under that directory.

INPUT: read {{expert.input}}.

TASK: using the skill's methodology, {{expert.task}}. Then write your deliverable to the artifact path given in your task prompt ({{expert.deliverable}}) using the Write tool.

HARD CONSTRAINTS:
- You are READ-ONLY on code: you may ONLY write inside the {{scope.artifactBase}}/ directory. Never edit source files.
- Never run installs, migrations or destructive commands.
{{expert.overrides.prompt}}
- Return a concise summary to the orchestrator: {{expert.summary}}.
```

> The two blank lines around `{{expert.overrides.prompt}}` are intentional: the
> overrides render as one or more constraint bullets; when absent, both lines
> collapse to nothing.

## Permission block (opencode `agent.<id>.permission`)

```json
{
  "edit": {
    "*": "deny",
    "{{scope.artifactBase}}/**": "allow"
  },
  "bash": {
    "*": "deny",
    "git *": "allow",
    "ls *": "allow",
    "find *": "allow",
    "cat *": "allow",
    "rg *": "allow"
  },
  "skill": {
    "*": "deny",
    "{{expert.skills}}": "allow"
  }
}
```

> `{{expert.skills}}` in the permission block renders one `"<skill>": "allow"`
> entry per skill (all experts write under the full `artifactBase` tree so they
> can also write shared-kernel artifacts under `sharedDir`).

## Invariants

- Read-only on source; writes only under the scope artifact base.
- Deny-all `skill` except the skills the expert owns.
- A failed load of a skill (e.g. tool unavailable in subagent context) is
  reported to the orchestrator, which falls back to injecting the skill's
  methodology into the delegation prompt — never a silent empty run.
