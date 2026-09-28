# agent-forwarding

SSH and GPG agent forwarding support for OpenCharly containers.

The `agent-forwarding` candy is a pure composition layer: it installs nothing of
its own but pulls in `gnupg`, `direnv`, and `ssh-client`, so a box can use the
host's GPG and SSH agent sockets (and the `.secrets` / direnv workflow). The
observable, verifiable effect is that the `gpg`, `ssh`, `ssh-add`, and `direnv`
client binaries are present in any box that composes it.

Forwarding itself is a **runtime** feature: agent sockets are bind-mounted from
the host into the container at `charly shell` / `charly start` (direct mode)
invocation time. It is intentionally excluded from quadlet mode, because agent
socket paths are session-bound and change between SSH sessions, reboots, and
users.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `agent-forwarding` |
| Composition | `gnupg`, `direnv`, `ssh-client` |
| Binaries | `/usr/bin/gpg`, `/usr/bin/ssh`, `/usr/bin/ssh-add`, `/usr/bin/direnv` |
| Service / port | none |
| Settings | `forward_gpg_agent`, `forward_ssh_agent` (both default `true`) |

## How to use it

Compose the layer in a box definition. A box is a `candy:` mapping that carries
the box's `base:` image and a nested `candy:` list of layer refs:

```yaml
my-box:
  candy:
    base: fedora          # the box's base image
    candy:                # the box's composition list
      - '@github.com/opencharly/layer-agent-forwarding:<tag>'
```

At runtime, through `charly shell` or `charly start` (direct mode), the host's
agent sockets are forwarded automatically:

```bash
ssh-add -l        # lists the host's SSH keys
gpg --version     # the gpg client that reaches the host GPG agent
```

Disable either channel globally with `charly settings set forward_gpg_agent false`
or `forward_ssh_agent false`, or per box in `charly.yml`.

## Layout

- `charly.yml` — the `agent-forwarding:` candy entity (composition, `plan:`
  checks) and the embedded `agent-forwarding-skill:` skill entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-distros:agent-forwarding`
- Components: `/charly-infrastructure:gnupg`, `/charly-coder:direnv`, `/charly-infrastructure:ssh-client`
- Runtime: `/charly-core:shell`, `/charly-core:service`
- Secrets workflow: `/charly-build:secrets`
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
