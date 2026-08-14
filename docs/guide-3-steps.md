# Guía en 3 pasos

## Paso 1 · Instalar

```bash
npx skills add harness-factory/harness-factory -g -y
```

Agrega el comando global `/factory` (ver `.opencode/INSTALL.md`) y reinicia
opencode.

## Paso 2 · Crear un harness desde un intent

En el repo donde querés el harness:

```bash
/factory "refactorizar el módulo de licenses hacia DDD"
```

El factory:
1. Declara qué capacidades usa (catálogo local / skills.sh / context7).
2. Propone un **roster** (expertos + executor + orquestador) basado en el intent
   y el stack del repo.
3. **Espera tu confirmación** — podés agregar o sacar expertos ("carrito de
   compras").
4. Si falta un skill requerido, **pausa** y te pide `npx skills add ...`.
5. Congela `harness.manifest.json` y ensambla el harness (skill del orquestador,
   agentes en `opencode.json`, comando, golden-rule en `AGENTS.md`).

## Paso 3 · Usar y regenerar

```bash
/ddd-plan licenses        # ejecuta el harness generado (o tu comando)
/factory build            # regenera el harness idéntico desde el manifest
```

`build` es la prueba de aceptación: el diff contra el harness existente debe ser
vacío.

---

Para más detalle: `docs/roles.md` (los roles fijos) y
`schema/harness.manifest.schema.json` (el contrato).
