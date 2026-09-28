# agentteams-higress

The Higress AI gateway for AgentTeams, as an OpenCharly candy.

Higress is the AI gateway in front of the AgentTeams Manager and Workers. This
candy ships the apiserver, controller, pilot, envoy gateway, and console
binaries extracted from the pinned `higress/all-in-one:2.2.1` image, re-declared
as five supervisord services that mirror the upstream priorities (200/300/400/500/600)
and environment. The console runs the extracted `higress-console.jar` on Java.

Every service runs rootless as the image user (uid 1000). The privileged paths
the upstream scripts write at runtime (`/etc/certs`, `/etc/istio/config`,
`/etc/istio/proxy`, `/var/lib/istio`, `/var/log/proxy`) are pre-created
uid-1000-writable at build time, and the start scripts are charly-owned — no
sudo, no upstream install scripts.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `agentteams-higress` |
| Binaries | apiserver, controller, pilot, pilot-agent, envoy, console JAR |
| Services / ports | `higress-apiserver` (200), `higress-controller` (300), `higress-pilot` (400), `higress-gateway` (500), `higress-console` (600) on `8080` (gateway) / `8001` (console) |
| Volume | `~/.agentteams/higress` |
| Packages | `openbsd-netcat`, `openssl`, `curl`, `jre-openjdk`, `libxcrypt-compat` (Arch) |

## How to use it

Compose the candy into a box (or the full AgentTeams stack, whose top
composition already includes it):

```yaml
my-agentteams:
  candy:
    base: cachyos.cachyos
    candy:
      - '@github.com/opencharly/pod-agentteams-higress:<tag>'
```

Then build and deploy with the charly CLI:

```bash
charly box build my-agentteams
charly start my-agentteams
```

See the owning skill for the composition, ports, and both deploy substrates.

## Layout

- `charly.yml` — the `agentteams-higress:` candy entity: the ten `extract`
  entries, the Arch package list, the volume, the two ports, the five services,
  and the plan that writes the charly-owned start scripts.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-agentteams:agentteams` — the full stack composition.
- Sibling services: `/charly-agentteams:agentteams` (matrix, minio, element, controller).
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
