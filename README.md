# agentteams-matrix

The AgentTeams Matrix homeserver, as an OpenCharly candy.

Matrix is where the AgentTeams Manager and Workers converse in shared Rooms
(chat spaces). This candy ships the Tuwunel homeserver — a conduwuit fork — as a
supervisord service on `:6167`, configured with the `CONDUWUIT_*` environment
the AgentTeams controller expects (registration token, room lifecycle cleanup,
display-name suffix off).

The service runs rootless as the image user (uid 1000) against the
`~/.agentteams/matrix` volume. The start script is charly-owned: it projects the
AgentTeams environment onto the `CONDUWUIT_*` vars and self-provisions the
registration token on the durable volume when one is not supplied.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `agentteams-matrix` |
| Binary | `tuwunel` (extracted from the pinned upstream image) |
| Service / port | `tuwunel` on `6167` |
| Volume | `~/.agentteams/matrix` |

Deploy-overridable `env_accept` vars set the Matrix server name
(`AGENTTEAMS_MATRIX_DOMAIN`, shared with the element and controller candies) and
the registration token (`AGENTTEAMS_REGISTRATION_TOKEN`).

## How to use it

Compose the candy into a box (or the full AgentTeams stack, whose top
composition already includes it):

```yaml
my-agentteams:
  candy:
    base: cachyos.cachyos
    candy:
      - '@github.com/opencharly/pod-agentteams-matrix:<tag>'
```

Then build and deploy with the charly CLI:

```bash
charly box build my-agentteams
charly start my-agentteams
```

See the owning skill for the composition, ports, and both deploy substrates.

## Layout

- `charly.yml` — the `agentteams-matrix:` candy entity: the `tuwunel` `extract`,
  `env_accept`, the volume, the port, the `tuwunel` service, and the plan that
  writes the rootless start script.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-agentteams:agentteams` — the full stack composition.
- Sibling services: `/charly-agentteams:agentteams` (element, minio, higress, controller).
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
