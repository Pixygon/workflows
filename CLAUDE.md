# workflows

<!-- pixygon:workflow-start v1 -->
## Pixygon workflow

This repo follows the Pixygon end-of-session workflow. After producing
shippable code in a prompted session:

1. **Visual check** (frontend repos only): `npm run visual-check` if the repo has
   one. AI vision verifies the page rendered. Non-zero exit blocks the ship.
2. **Ship**: `POST /v1/ship/<project-id>` with entries + optional minutes. One
   call appends changelog entries, logs timesheet (auto-skips if off), bumps
   version, releases, fans out the orchestrator, returns the rendered
   `CHANGELOG.md` inline.
3. **Commit + push** including `CHANGELOG.md` in the same commit.

**Project ID**: `<unknown — set via PixygonServer admin or in this repo's .pixygon.json>`

Full spec: [`Dyson/docs/WORKFLOW.md`](../Dyson/docs/WORKFLOW.md). System map:
[`Dyson/docs/workflows/registry.yml`](../Dyson/docs/workflows/registry.yml) — also
rendered as the Atlas tab in Dyson.

_This section is managed by `Dyson/scripts/standardize-repos.mjs`. Don't edit
by hand — change the script or the canonical doc instead._
<!-- pixygon:workflow-end -->

