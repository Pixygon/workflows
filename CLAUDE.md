# workflows

<!-- pixygon:workflow-start v1 -->
## How to finish a prompted session (MANDATORY)

When you finish a piece of prompted work in this repo that produces
shippable code, run **`pearl ship`** from the repo root. That is THE
final step of the session — don't commit by hand, don't curl the
release endpoints yourself, don't end the session without running it.
One command, every time.

```
pearl ship
```

If `pearl` isn't on PATH:

```
node /home/pixygon/repos/Dyson/scripts/pearl.mjs ship
```

### What `pearl ship` does

1. Reads `.pixygon.json` (here or any parent dir) for the project ID.
2. Runs `scripts/visual-check.mjs` if present (frontend repos).
3. AI-drafts changelog entries from your git diff.
4. POSTs to `/v1/ship/<project-id>` — appends entries, logs the
   timesheet entry (auto-skip if off), bumps version, releases the
   changelog, fans out the orchestrator side-effects.
5. Writes the returned `CHANGELOG.md` to the repo root.
6. Runs `scripts/post-ship.mjs` if present (e.g. translation sync).
7. `git add -A && git commit && git push` — AI headline as message.

If the drafter classifies your diff as 100% plumbing (refactor, dep
bump, no behaviour change), pearl skips the release and just commits +
pushes. Still one command.

### Auth — zero setup required

Pearl auto-reads `~/.config/dyson-swarm/config.toml` for the API key.
You don't need to set `PIXYGON_API_KEY` manually. Override the search
order via env (`PIXYGON_API_KEY`) or a Pearl-specific
`~/.config/pearl/auth.json`.

### Flags

- `--review` — print drafted entries, prompt before `/ship`
- `--no-test` — skip `scripts/visual-check.mjs`
- `--no-commit` — ship + write `CHANGELOG.md` but don't `git commit`
- `--magnitude=patch|minor|major` — override AI's auto bump
- `--minutes=N` — log N minutes to timesheet (silent skip if off)
- `--message="..."` — override the AI commit message
- `--dry` — print every step, send nothing

### Don't use partial-flow primitives directly

`POST /v1/changelogs/project/:id/append`, `/timesheet/...`,
`/changelogs/project/:id/bump` etc. exist for backfill + admin repair.
Reach for them only when `pearl ship` genuinely doesn't fit.
Otherwise: `pearl ship`.

**Project ID**: `<unknown — set via PixygonServer admin or in this repo's .pixygon.json>`

Full spec: [`Dyson/docs/WORKFLOW.md`](../Dyson/docs/WORKFLOW.md). System map:
[`Dyson/docs/workflows/registry.yml`](../Dyson/docs/workflows/registry.yml) — also
rendered as the Atlas tab in Dyson.

_This section is managed by `Dyson/scripts/standardize-repos.mjs`. Don't edit
by hand — change the script or the canonical doc instead._
<!-- pixygon:workflow-end -->

