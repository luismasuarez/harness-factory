# Template: slash command (rendered per harness)

Rendered into the target project's `opencode.json` under `command`.

Placeholders: `{{command.name}}`, `{{command.description}}`,
`{{command.agent}}`, `{{command.template}}`.

```json
{
  "command": {
    "{{command.name}}": {
      "description": "{{command.description}}",
      "agent": "{{command.agent}}",
      "template": "{{command.template}}"
    }
  }
}
```

> `$ARGUMENTS` in `template` is replaced with the command's arguments at runtime.
> The rendered command must be merged into the target project's existing
> `opencode.json` — it does not replace the file.
