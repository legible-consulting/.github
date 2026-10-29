# .github — the organisation's public defaults and reusable workflows

This repo holds three things for the `legible-consulting` organisation. It is public because GitHub serves defaults and reusable workflows only from a public `.github` repo. `README.md` says the same.

## Routing

| You are asked to | Go to |
|---|---|
| change what every client repo checks on push and on a pull request | `.github/workflows/check-client.yml`, then its row in `.github/workflows/README.md` |
| change the pull request's contract or its merge gate | `.github/workflows/gate.yml`, then its row in the README |
| change the release pull request | `.github/workflows/release.yml`, then its row in the README |
| change this repo's own check | `.github/workflows/ci.yml` |
| change the organisation's front page | `profile/README.md` |
| change the default security or contribution policy | `SECURITY.md`, `CONTRIBUTING.md` |

Read `.github/workflows/README.md` before you change a workflow. Its principles and its cost table are the rules.

## Rules

- A change to a reusable workflow reaches every client repo that calls it at `@main` on its next run. Update the README's row and cost table in the same pull request (why: the README is the one place a reader learns what each job does and costs).
- CI is truth: never run a check locally. `ci.yml` runs the changed workflows on their own pull request (why: one verdict, recorded where the code was pushed).
- This repo is public. Never write a secret, a client's name or a client's words here (why: anyone can read it, and a secret in git outlives every rotation).
- Gates are human checkboxes: read them, never tick them. Jamie merges (why: a gate is where Jamie's judgement enters).
