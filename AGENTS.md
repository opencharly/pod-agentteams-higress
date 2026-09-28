# AGENTS.md — pod-agentteams-higress

Standalone candy repo for the `agentteams-higress` candy — the Higress AI
gateway (apiserver, controller, pilot, envoy gateway, console) the full
AgentTeams stack composes. The entire candy lives in `charly.yml` at the repo
root: the `agentteams-higress:` entity with its `extract` list, five service
declarations, and a `plan:` that writes the charly-owned start scripts. There is
no source tree — the binaries are extracted from the pinned upstream image at
build time.

Canonical files:

- `charly.yml` — the `agentteams-higress:` candy entity (description, `extract`,
  distro packages, volume, ports, `service`, `plan`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-agentteams:agentteams` — the owning skill for the AgentTeams stack
  (the five-service composition, volumes, ports, both deploy substrates). Load
  before editing, building, deploying, or troubleshooting this candy.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, package sections, service declarations).
  Load before editing any entity field or plan step.
- `/charly-internals:git-workflow` — before any git/PR action.

The candy carries no `skill:` entity of its own; the family skill
`/charly-agentteams:agentteams` covers the surface. The gap is routed to the named skill-authoring
batch [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291);
when that lands, add the owning skill here.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The live R10 witness is the stack's `check-agentteams-pod` bed
  (`charly check run check-agentteams-pod`); there is no higress-only bed.

## Modify this repo

- Edit the `agentteams-higress:` candy entity in `charly.yml`. The five
  charly-owned start scripts are authored inline in the `plan:`; a change to a
  service's env, priority, or runtime path belongs there.
- The rootless posture is load-bearing: the privileged paths the upstream
  scripts expect must be pre-created uid-1000-writable in the `plan:` — a new
  runtime write path needs the same treatment.
- The `description:` and `plan:` `check:` steps are the verifiable contract
  (binary on disk, live HTTP/status from the running gateway). Keep them true.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
