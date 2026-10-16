# Legible Consulting

AI consultancy run by Jamie Nisbet from Mafra, Portugal. We build sites, apps and platforms
for clients, and we teach the way we build them.

## What we do

- **Build.** A client site, app or platform, delivered through one pipeline: brief and
  tickets in the repo, a preview environment the client signs off before every release,
  live status they can read at any time, and a handover pack at the end.
- **Teach.** The Interpretable Context Methodology (ICM) in a one-day workshop, and coaching
  that leaves a client with their own working workspace.

## How we work

The folder tree is the orchestration. Every repository we ship carries a short `CLAUDE.md`
that says what the repo is and where to go, and every stage folder carries a `CONTEXT.md`
contract with its Inputs, Process and Outputs. An agent with no memory can open the repo
cold, orient, act and report from the files alone. Gates are human; a release is a merge
Jamie makes by hand; CI is the source of truth.

## What is public here

| Repository | What it is |
|---|---|
| [blueprint](https://github.com/legible-consulting/blueprint) | The template every client build is rendered from (`site`, `app`, `platform`, `workspace`), the conventions those repos keep, and the workshop kit. |
| [.github](https://github.com/legible-consulting/.github) | This page, the reusable CI workflows client repos call, and the default community files. |

Client repositories are private unless the client prefers otherwise. The operating
repository that runs the business is private and never shared.

## Working with us

- Clients and enquiries: [jamienisbet.com](https://jamienisbet.com)
- Collaborators on a client build: you will be invited to one repository; see
  [CONTRIBUTING](https://github.com/legible-consulting/.github/blob/main/CONTRIBUTING.md).
- Security reports: see [SECURITY](https://github.com/legible-consulting/.github/blob/main/SECURITY.md).
