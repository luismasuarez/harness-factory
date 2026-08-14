# Catalog

Role descriptors: declarative data that turns skills into **expert candidates**.
This is the "talent pool" — each entry wraps a skill with the metadata the
factory's selector matches against the intent.

## Format

Each catalog file is a JSON array of entries:

| Field | Purpose |
|---|---|
| `skill` | Name of the skill (as loaded via the skill tool). |
| `roleLabel` | Human label for the expert role. |
| `trigger` | Keywords the selector matches against the intent. |
| `skillSource` | Install source (same shape as `skills-lock.json`), used for `npx skills add`. |
| `deliverable` | Default artifact filename this expert produces. |
| `description` | One-line description (also used for routing). |
| `additionalSkills` | Optional extra skills the expert loads. |

## Conventions

- `catalog/` files are **declarative data only**. The selection logic lives in
  `skills/harness-factory/SKILL.md`, never in the catalog.
- To make a new skill selectable, add a descriptor here (or a catalog file in
  the target repo). This is the abstraction that makes the whole ecosystem
  candidates: *wrap the skill → it becomes an expert*.
- Seed files: `seed-ddd-architecture.json` (the six DDD experts) is the
  reference example and the dogfooding source.
