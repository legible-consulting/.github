# Reusable workflows (Wave 1)

Called from every client repo's `ci.yml` as
`legible-consulting/.github/.github/workflows/<file>@<ref>`. Callers pass secrets explicitly.

| File | Does | Secrets from the caller |
|---|---|---|
| `check-client.yml` | per-tier lint, typecheck, test, gitleaks, actionlint; input `tier` | none |
| `deploy-vercel.yml` | `vercel pull`, `vercel build`, `vercel deploy --prebuilt`; preview on PR (the UAT environment), production on the release merge | `VERCEL_TOKEN`; variables `VERCEL_ORG_ID`, `VERCEL_PROJECT_ID` |
| `release.yml` | release-please opens the release PR; merging it is the release | `GITHUB_TOKEN` with PR permission |
| `guard-main.yml` | on push to `main`: fails when the commit is not a PR merge (GitHub emails the failure) | none |

Why here and not a `workflows` repo (2026-10-15): one public repo already exists and may hold
any content; a second one is a second place to tag and watch.
