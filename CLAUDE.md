# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

A **composite GitHub Action** (`aziontech/scc_check`) that measures code
complexity and lines-of-code changes on pull requests using
[SCC](https://github.com/boyter/scc), then posts the results as a PR comment.
There is no application code, build step, or test suite — the entire
deliverable is the YAML/shell in `action.yml`.

Consumers reference it as `uses: aziontech/scc_check@main` (see `README.md`).

## Layout

- `action.yml` — the published composite action. This is the source of truth; changes here are what downstream repos consume.
- `.github/workflows/scc_check.yml` — a self-test workflow that duplicates the action's logic inline (not via `uses:`), so it can be exercised against this repo's own PRs before the action is imported elsewhere. **Keep it in sync with `action.yml`** — the two contain near-identical step definitions.
- `README.md` — consumer-facing usage docs.

## How the complexity check works

The action runs on `pull_request` and compares the base branch against the head branch:

1. Install `jq`, `curl`, and download the latest SCC release into `$HOME/bin` (added to `PATH` via `$GITHUB_ENV`).
2. Checkout `github.base_ref`, run `scc --format=json`, sum `.Complexity` and `.Code` across all entries → `TARGET_COMPLEXITY` / `TARGET_CODE`.
3. Checkout `github.head_ref`, compute the same → `SOURCE_COMPLEXITY` / `SOURCE_CODE`.
4. Diff source − target and post a markdown table comment via `actions/github-script`.

State is passed between steps through environment variables written to `$GITHUB_ENV` (not step outputs, despite the `id:` labels on the steps).

Requires the consumer's checkout to use `fetch-depth: 0` so both branches are available.

## Working on this repo

- There are no local build/lint/test commands. Validate changes by editing `action.yml` (and mirroring into the workflow), then opening a PR against `main` — the `SCC Complexity Check` workflow runs and comments on the PR, which is the effective integration test.
- To reproduce the metrics locally: install `scc`, then `scc --format=json | jq -r '[.[].Complexity] | add'` (or `.Code`) — this is exactly what the action sums.
- The action pins nothing to a fixed SCC version; it always fetches `releases/latest`. Only the Linux x86_64 asset is downloaded, so the action assumes an `ubuntu-latest` / apt-based runner.

## Differences between `action.yml` and the workflow to watch for

- `action.yml` uses `github-token: ${{ inputs.github_token }}` (defaults to `github.token`); the workflow uses `${{ secrets.GITHUB_TOKEN }}`.
- The workflow has extra `Debug ... SCC` steps that run bare `scc`; the action omits them.
- The action declares `shell: bash` on each run step (required for composite actions); workflow steps don't need it.
