# Reusable workflow example

This repository shows how to call a reusable GitHub Actions workflow that applies PR labels.

## Layout
- `.github/workflows/reusable-infra.yml` defines the reusable workflow exposed through `workflow_call`.
- `.github/workflows/main.yml` triggers on pull requests (and `workflow_dispatch`) and reuses the workflow.
- `.github/labeler.yml` holds the configuration consumed by `actions/labeler` (v6+ YAML structure).

## What happens
1. When a pull request targets `main` (or branches under `features/`), or when you run the workflow manually, GitHub executes the caller workflow.
2. The caller forwards the labeler config path together with optional metadata (`environment`, `component`, `auto-apply`) and passes its `GITHUB_TOKEN` as the required secret.
3. The reusable workflow executes shared stages (initialize → validate → plan → optionally apply changes) before running `actions/labeler@v6`, which applies labels based on `.github/labeler.yml`.

Remember that GitHub limits `GITHUB_TOKEN` to read-only permissions on pull requests coming from forks, so labeling will only succeed for branches within the same repository.
