# Install — Harness Factory (opencode)

Follow these steps in order. The factory is a skill that generates harnesses in
any repo you work in.

## 1. Install the skills

```bash
npx skills add harness-factory/harness-factory -g -y
```

This installs the `harness-factory` skill (and its companion `harness-runtime`)
into `~/.config/opencode/skills/`, so they are available in every project.

> If the repo isn't published on skills.sh yet, install from the repo directly:
>
> ```bash
> npx skills add /path/to/this/repo -g -y
> ```
>
> or clone it and point opencode at the `skills/` directory.

## 2. Register the global `/factory` command

Add to your `~/.config/opencode/opencode.json`:

```json
{
  "command": {
    "factory": {
      "description": "Generate a harness from a natural-language intent",
      "template": "Load the harness-factory skill and run the factory pipeline for: $ARGUMENTS"
    }
  }
}
```

(Or create `.opencode/command/factory.md` in a project to scope it.)

## 3. Restart opencode

Skills and commands are discovered at startup. Restart opencode so the
`/factory` command and the `harness-factory` skill load.

## 4. Verify

```bash
# in any repo:
/factory "refactorizar X hacia DDD"
```

You should see the factory declare its capabilities, propose an expert roster,
and STOP for your confirmation before writing a manifest.

## 5. First harness

Follow the guide at `docs/guide-3-steps.md`. For a worked example, see
`examples/ddd-architecture/`.

---

> **Multi-agent ready**: this repo ships empty `.claude-plugin/` and
> `.codex-plugin/` directories. OpenCode is the reference implementation; other
> harnesses are a later milestone.
