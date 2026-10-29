# Reusable workflows

Called from every client repo as
`legible-consulting/.github/.github/workflows/<file>@<ref>`. Callers pass secrets explicitly.

| File | Does | Secrets from the caller |
|---|---|---|
| `check-client.yml` | **live.** Called from `ci.yml` on push to `main` and on pull requests. Three jobs: `secrets` (gitleaks, full history, every run. It also reads the changed files for the two other jobs. A build rendered from blueprint `v1.2.0` or later must have `.icm/environments.md`, with a table whose header row has the columns `Environment`, `Address`, `Vercel project`, `Database`, `Moves on`. A workspace and a repo with no `.copier-answers.yml` have no such check), `workflows` (actionlint, when a file under `.github/workflows/` changed), `code` (pull requests only. A build: lint, typecheck, test if `package.json` defines them, npm or pnpm by lockfile, when a changed file is outside `.icm/**`, `**/*.md` and `.github/**`. Before them, `check:migrations` if `package.json` defines it, when `.copier-answers.yml` says `database: neon` and a changed file is under its `migrations_path`. No format check, and no lint flag: a zero-warning limit goes in the repo's own `lint` script. A client's ICM workspace, `with: { workspace: true }`: every `CONTEXT.md` keeps Inputs, Process, Outputs, when a `CONTEXT.md` changed). A build passes no input | none |
| `gate.yml` | **live.** Called from a build's `gate.yml` on pull requests, with the types `opened`, `edited`, `synchronize`, `ready_for_review`, `reopened`. Two jobs, bash only: `contract` (the ticket line, the gate anchors, and `.icm/intake/` against the intake contract; `setup/` is its own category, beside `triage/` and each epic) and `gate` (green on the Ready to merge tick). Both pass with no check when the repo has no `.icm/intake/CONTEXT.md` | none |
| `release.yml` | **live.** Input `release-type` (default `node`). One job `release-please` on push to `main`: release-please opens or updates the release PR. Merging it is the release (tag, `CHANGELOG.md`, GitHub release). The caller's job grants `contents: write`, `pull-requests: write` | `token`: the caller's `GITHUB_TOKEN`, or a fine-grained PAT when the release PR must run checks (a PR opened with `GITHUB_TOKEN` starts no workflows) |

`ci.yml` is this repo's own check, not a reusable workflow. It calls `check-client.yml` and
`gate.yml` from the same commit, with no input. A change to either file checks itself on its
pull request. Here `gate.yml` finds no `.icm/intake/CONTEXT.md`, and its jobs pass with no check.

A called workflow can only narrow the caller's permissions: the caller's job grants what the
table names. The header comment of each file shows the caller's job.

Why here and not a `workflows` repo (2026-10-15): one public repo already exists and may hold
any content. A second one is a second place to tag and watch.

## Principles

1. **The deploy is the verdict and costs no minute.** Vercel builds every push to `main` and
   every pull request. CI does not run the build again (why: the build that ships is the one
   that counts, and Vercel's minutes are not GitHub's).
2. **A check must be fixable without a local run.** No local check runs, so a session reads
   the error in CI and fixes it. Formatting is never a verdict (why: a format diff has no
   error line to fix from, and the formatter is a local run).
3. **A private repository is billed per job, rounded up to a minute.** A job of ten seconds
   costs one minute (why: every client repo is private, on GitHub Free).
4. **One shape for every repo.** Every build calls the same workflows from here, with no input
   (why: one fix here reaches every repo, and a reader learns one shape).
5. **Nothing fires to be safe.** A new job needs one sentence that says what it costs and what
   it buys. A job that buys nothing on a run skips on that run (why: a check that runs "just
   in case" costs a minute on every push and finds nothing).

What each job costs and buys:

| Job | Costs | Buys |
|---|---|---|
| `secrets` | One minute on each push to `main` and each pull request. | A leaked key is red before the merge. Private repos on GitHub Free get no secret scanning. The changed files for `workflows` and `code`, with no job of their own. A build with no `.icm/environments.md`, or with a column missing from its table, is red. |
| `workflows` | One minute when a workflow changed. Nothing otherwise. | A broken workflow is red on the change, not on the day it is needed. |
| `code` | One minute or more on a pull request that changes code. Nothing on `main` and on a change to tickets, docs or workflows. A change to the migrations also fetches the full history. | Lint, typecheck and test before the merge. A migration out of order is red before the merge. Drizzle records such a migration as applied and never runs it. |
| `contract` | One minute on each pull request event, also each edit of the body. | The body, the ticket and `.icm/intake/` agree with the intake contract before the merge. |
| `gate` | One minute on each pull request event, also each edit of the body. | No merge to `main` before Jamie's Ready to merge tick. |

## The ruleset

The ruleset of each client repo requires the check `gate / gate` on `main`. The kickoff sets
the ruleset. That is Jamie's hand.

Require `gate` only. Never require `code` (why: a job that its `if` skips reports success, so
a required `code` proves nothing on a pull request that changes no code. A workflow that a
`paths` filter keeps from starting never reports, and a required check from it blocks the
merge).

`gate` is in its own caller, `gate.yml`, not in `ci.yml` (why: the tick is an edit of the body.
An edit then runs the two small jobs only, never the build. A run of `ci.yml` on an edit would
put a skipped `code` check over a red one on the same commit).

`gate` is green on release-please's pull request and on Dependabot's. Neither carries a box. A
person still merges each, and `merge.sh` still refuses without the tick. release-please opens
its pull request with `GITHUB_TOKEN`, and that starts no workflow. Then `gate` does not report,
and the ruleset blocks the release pull request. Pass a fine-grained PAT as `release.yml`'s
`token`, or give the merge of the release pull request a bypass in the ruleset.
