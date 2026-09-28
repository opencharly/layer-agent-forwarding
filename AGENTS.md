# AGENTS.md — layer-agent-forwarding

Standalone candy repo for the `agent-forwarding` layer. The whole candy lives in
`charly.yml` at the repo root: its `candy:` composition, its ordered `plan:` of
build-time `check:`/`agent-check:` steps, and the embedded `skill:` entity
projected into the marketplace corpus as `/charly-distros:agent-forwarding`.
There is no source tree and no service of its own — the candy is a pure
composition of `gnupg`, `direnv`, and `ssh-client`.

Canonical files:

- `charly.yml` — the `agent-forwarding:` candy entity and the
  `agent-forwarding-skill:` skill entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-distros:agent-forwarding` — the owning skill: the composition, the
  runtime socket-forwarding model, and the settings/per-box overrides. Load
  before editing or troubleshooting the candy.
- `/charly-core:charly-config` — the `forward_gpg_agent` / `forward_ssh_agent`
  settings the skill documents. Load when touching forwarding configuration.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`/`agent-check:`, composition). Load before
  editing any entity field or plan step.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
  Keep the `version:` schema stamp within the installed charly's supported range.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has no
  per-repo candy gate.
- There is no live bed: the candy is a composition, so the evidence is its
  `plan:` steps — the `gpg`, `ssh`, `ssh-add`, and `direnv` binaries exist and
  their packages are installed. The `agent-check:` step is exercised only where
  a host agent socket is forwarded at runtime.

## Modify this repo

- Edit the `agent-forwarding:` candy entity AND the `agent-forwarding-skill:`
  skill entity in `charly.yml` together. The skill is the projected usage
  source, so a composition or behaviour change not mirrored in the skill leaves
  the corpus stale.
- Keep per-tool shell init in the owning tool's candy (`direnv`, `keepassxc-keyring`),
  not here — this candy stays a declarative composition.
- Keep `version:` at the schema stamp the pinned CI charly supports.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
