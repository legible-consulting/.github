# Reusable workflows

Called from every client repo's `ci.yml` as
`legible-consulting/.github/.github/workflows/<file>@<ref>`. Callers pass secrets explicitly.

| File | Does | Secrets from the caller |
|---|---|---|
| `check-client.yml` | **live.** Three jobs: `secrets` (gitleaks, full history), `workflows` (actionlint), `code` (a build: lint, typecheck, test if `package.json` defines them, npm or pnpm by lockfile. A client's ICM workspace, `with: { workspace: true }`: every `CONTEXT.md` keeps Inputs, Process, Outputs). A build passes no input | none |
| `release.yml` | **live.** Input `release-type` (default `node`). One job `release-please` on push to `main`: release-please opens or updates the release PR; merging it is the release (tag, `CHANGELOG.md`, GitHub release). Jamie merges it. The caller's job grants `contents: write`, `pull-requests: write` | `token`: the caller's `GITHUB_TOKEN`, or a fine-grained PAT when the release PR must run checks (a PR opened with `GITHUB_TOKEN` starts no workflows) |

A called workflow can only narrow the caller's permissions: the caller's job grants what the
table names. The header comment of each file shows the `ci.yml` job that calls it.

Why here and not a `workflows` repo (2026-10-15): one public repo already exists and may hold
any content; a second one is a second place to tag and watch.
