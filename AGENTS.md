# AGENTS.md

## Cursor Cloud specific instructions

RenderCV is a single Python CLI package (`src/rendercv`) that turns a YAML CV into a
PDF/HTML/Markdown/PNG using Typst. There is no server, database, or long-running
service — everything runs as a local `rendercv` CLI invocation, so there is nothing
to "start" for testing.

Tooling: dependencies are managed with `uv` and tasks are run via `just` (see
`justfile`). Both are installed to `~/.local/bin` (already on `PATH` via `~/.bashrc`).
Python 3.12+ is required. The update script runs `just sync` (= `uv sync --frozen
--all-extras`) to create/refresh the `.venv`.

Common commands (all defined in `justfile`):
- Tests: `just test` (pytest, parallel via xdist).
- Lint/type: `uv run --frozen --all-extras ruff check src tests` and
  `uv run --frozen --all-extras ty check src tests` both pass on the pinned versions.
- Build docs: `just build-docs` (mkdocs, writes to `site/` which is gitignored).
- Run the app: `uv run rendercv new "Name"` then `uv run rendercv render "Name_CV.yaml"`.

Gotcha — `just check` currently fails, but NOT because of the environment:
its final step `prek run --all-files` runs a `ty-check` pre-commit hook that installs
the *latest* `ty` in its own isolated venv (ignoring the pinned `ty==0.0.5` in
`pyproject.toml`). The newer `ty` reports version-drift diagnostics (e.g.
"unused ignore comment") on code that passes with the pinned `ty`. Treat this as a
pre-existing tool-version drift issue in the repo, not a setup problem; run `ruff` and
the pinned `ty` directly (as above) for reliable lint/type results.

The CLI pings PyPI on startup to check for a newer version; this is non-blocking and
prints a harmless "A new version of RenderCV is available" notice offline/online.
