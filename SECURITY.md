# Security Policy

## Reporting a vulnerability

Please **do not open a public issue** for security problems. Report them privately
using [GitHub Private Vulnerability Reporting](https://github.com/luismasuarez/harness-factory/security/advisories)
(or email the maintainers directly if the repo is not yet public).

We aim to acknowledge reports within 48h and triage them within a week.

## Hard rules for contributors

This standard runs inside a coding agent's environment. Session transcripts and
local config can contain real credentials. **These rules are non-negotiable:**

1. **Never commit session transcripts.** Raw session dumps (design records,
   tool logs, `opencode.json` dumps, MCP config) routinely embed API keys, tokens
   and personal config. If you want to preserve design rationale, write a
   **curated** decision record — no raw logs, no config values.
2. **Never commit secrets.** No API keys, tokens, `.env`, or cloud credentials.
   Keys live in environment variables or the agent's secret store, never in the
   repo. Scan before pushing (`rg -i "sk-|ghp_|ctx7sk|api[_-]?key" .`).
3. **Assume anything pushed to a public repo is compromised forever.**
   Even a history rewrite does not guarantee erasure (GitHub may retain objects,
   forks/clones exist). If a secret leaks, **rotate it** — do not rely on a purge.
4. **Do not add dependency installs** (npm/pip/apt) to harnesses without approval;
   generated harnesses must never assume extra tooling beyond what the target
   project already has.

## Scope

The repo itself, the skills it ships, and the harnesses it generates. The
generated harnesses inherit the permissions granted in each project's
`opencode.json`; users are responsible for scoping executor `writePaths` and
expert read-only access.
