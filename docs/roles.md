# Roles del estándar

El estándar define **3 roles fijos** (plantillas en `templates/roles/`). El
manifest solo decide *cuántos expertos* y *qué scopes*, nunca cambia el modelo
de seguridad.

## 1 · Analysis Expert (read-only)

- **Qué hace**: analiza y escribe artefactos de diseño bajo el scope
  (`docs/architecture/<scope>/`). NUNCA edita código fuente.
- **Skill**: carga exactamente el skill que el manifest le asigna.
- **Permisos** (`edit`): deny-all salvo `docs/architecture/<scope>/**`.
- **Garantía**: si un experto no puede cargar su skill, el orquestador inyecta la
  metodología en el prompt de delegación — nunca un run vacío.

## 2 · Executor (write-scoped)

- **Qué hace**: aplica los slices del blueprint, corre los gates y commitea.
- **Permisos** (`edit`): deny-all salvo los `writePaths` declarados.
- **Reglas duras**: stage solo paths del slice (+ el blueprint); nunca
  `git add -A`, nunca amend, nunca push. Gate en rojo → revert del slice.
- Es el **único** agente (además del orquestador) que escribe código.

## 3 · Orchestrator (primary)

- **Qué hace**: delega a los expertos en orden (Task DAG), valida artefactos,
  sintetiza el plan, espera aprobación y orquesta la ejecución por slices.
- **Permisos** (`task`): deny-all salvo `expert-*` y el executor.
- **Reglas duras**: no edita antes de la aprobación; un slice = un commit;
  nunca parallelizar fases; nunca saltar gates.

## El runtime fijo (invariantes)

Aplica a todo harness, siempre (definido en `skills/harness-runtime/SKILL.md`):

| Invariante | Descripción |
|---|---|
| Baseline gate | typecheck + lint + test + build verdes antes de tocar nada; tripwire si falla. |
| Aprobación | El plan se presenta y espera OK explícito antes de cualquier edición. |
| Slices | Behavior-preserving primero, characterization si cobertura baja, refactor separado de redesign. |
| Gates por slice | Tras cada slice, gates; rojo → revert inmediato. |
| Disciplina de artefactos | Nunca sobreescribir artefactos de otro scope. |
| Denials verbatim | Un permiso denegado se reporta textual, nunca se trabaja por afuera. |
