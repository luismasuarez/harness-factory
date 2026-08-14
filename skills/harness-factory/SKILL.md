---
name: harness-factory
description: >
  Generate agent harnesses from natural-language intent using the agent skills
  ecosystem as the expert talent pool. Use when the user wants to create a new
  harness (intent-driven), regenerate/rebuild an existing harness from its
  manifest, or validate an existing harness. Two modes: `factory <intent>`
  proposes an expert roster for explicit confirmation then freezes a
  harness.manifest.json and assembles the harness (orchestrator skill, agents,
  command, AGENTS.md golden-rule); `factory build` regenerates from an existing
  manifest. This is the ONLY entry point for creating or regenerating harnesses
  — never hand-write harness agents/skills/commands ad hoc.
---

# Harness Factory

You are the **factory**: you turn an intent in natural language into a complete,
reproducible agent harness, using installed skills as the expert talent pool.

The factory has **two modes**:

- **`factory <intent>`** — dynamic: interpret → discover → propose roster →
  confirm → freeze manifest → assemble.
- **`factory build`** — static: regenerate an identical harness from an existing
  `harness.manifest.json` (reproducibility / drift fix).

## Golden rules (non-negotiable)

1. **Manifest is the source of truth.** Every harness is fully described by its
   `harness.manifest.json`, validated against `schema/harness.manifest.schema.json`.
   Never assemble from ad-hoc state.
2. **Factory proposes, user disposes.** Always propose the roster (experts +
   executor + orchestrator) and wait for explicit confirmation before freezing
   the manifest. "Carrito de compras" gate.
3. **Missing skills pause, never silently degrade.** If the intent requires a
   skill that isn't installed, PAUSE and ask the user to
   `npx skills add <source>`. Do not substitute a generic expert silently.
4. **Fixed runtime, variable team.** The security model (read-only experts,
   scoped executor, baseline gate, approval gate, slices, tripwire) is FIXED and
   defined in `skills/harness-runtime/SKILL.md`. The manifest only declares the
   variable parts. Never emit a harness that weakens the runtime.
5. **Reproducible.** `build` from a manifest must produce the identical harness.
   Diff-of-equivalence against the existing harness is the acceptance test.
6. **Follow the agents.md open format.** Golden-rule blocks emitted into target
   projects follow the Agentic AI Foundation `agents.md` standard.

## Load order

When invoked, FIRST:
1. Load this skill's companion: `skills/harness-runtime/SKILL.md` (the fixed
   invariants — read them and carry them into every harness you assemble).
2. Read `schema/harness.manifest.schema.json` (the contract you emit into).
3. Read `templates/roles/*.md`, `templates/pieces/*.md`, `templates/blocks/*.md`
   (the render sources).

## Mode A · `factory <intent>`

### Step 1 · Interpret
Read the intent. Infer what the task is (re-architecture, greenfield,
audit, ...) and, from the repo, the stack (`package.json`, source layout).

### Step 2 · Discover
Build the candidate pool:
- **Local catalog**: installed skills — from `skills-lock.json` and
  `.agents/skills/` (project) and `~/.config/opencode/skills/` (global), plus
  any descriptors in this repo's `catalog/`.
- **Ecosystem (optional, never blocking)**: `npx skills search <keywords>` for
  skills that match the intent. If unavailable/offline, fall back to the local
  catalog alone and say so.
- **Docs (optional, never blocking)**: context7 MCP for latest API docs. If not
  configured, rely on each skill's bundled references.

Declare at the start which capabilities you used
("modo: catálogo local + docs empaquetadas").

### Step 3 · Propose roster (GATE — explicit confirmation)
Match the intent's keywords against each skill's descriptor (`trigger`,
`description`) and the repo stack. Propose:

- **Experts** — one per selected skill: `id` (`expert-<k>`), `role`
  (`analysis-expert`), `skill`, `skillSource`, `deliverable`, `input`,
  `readPaths`, `trigger`.
- **Executor** — `id` (`executor`), `writePaths`, `bash`, `gates`.
- **Orchestrator** — agent name, baseline gates, pipeline (ordered Task DAG),
  synthesis deliverable, execution mode.

Present the roster as a table and **STOP**. The user may add/remove experts or
adjust scopes. Do NOT freeze the manifest or assemble until the user confirms.

### Step 4 · Freeze manifest
On confirmation, write `harness.manifest.json` in the target project (or a
specified path) and validate it against the schema. If validation fails, fix the
manifest before proceeding.

### Step 5 · Assemble
From the manifest + templates, generate:
1. **Orchestrator skill** — `skills/<harness.name>/SKILL.md` (or the project's
   skill dir) rendered from `templates/pieces/orchestrator-skill.md`, with the
   roster table + pipeline embedded.
2. **Agents** — merge into the project's `opencode.json`:
   - one `analysis-expert` agent per expert (from `templates/roles/analysis-expert.md`),
   - the `executor` agent (from `templates/roles/executor.md`),
   - the `orchestrator` agent (from `templates/roles/orchestrator.md`, `mode: primary`).
3. **Command** — the slash command from `templates/pieces/command.md`.
4. **Golden-rule block** — appended to the project's `AGENTS.md` from
   `templates/blocks/agents-golden-rule.md`.
5. **Scaffolding** — the scope artifact root (e.g. `docs/architecture/`) if absent.

## Mode B · `factory build`

- Read the existing `harness.manifest.json`, validate it against the schema.
- Regenerate steps 1–5 of Assemble exactly as Mode A.
- **Acceptance test**: diff the freshly generated harness against the current
  state of the target project. A harness is correct when the diff is empty
  (permits, prompts, SKILL.md, command). Fix templates until equivalence.
- If the manifest predates a standard change, migrate it and report the delta.

## Validation checklist (both modes)

After assembling, verify the target `opencode.json` is valid JSON and the
`$schema` reference resolves. If the target is opencode, note that a reload is
needed for agents/commands to take effect. Point the user to a quick
smoke test: run the new command with a tiny scope.

## Failure handling

- **No matching skills for the intent** → propose the closest catalog matches,
  and if none fit, PAUSE and suggest `npx skills search`; do not invent experts.
- **A required skill isn't installed** → PAUSE, report the exact
  `npx skills add <owner/repo>` (or pack) to run, and wait for confirmation that
  it was installed before proceeding.
- **Manifest invalid** → stop, report the schema violations verbatim.

## Repo conventions

- `catalog/` holds role descriptors (declarative data). The selection logic
  lives HERE, not in the catalog files.
- `templates/` are fixed; edit them only together with the schema and
  `harness-runtime` (they must stay in sync).
- This repo's `AGENTS.md` documents the invariants; follow it.
