# AGENTS.md

## Overview
Infrastructure-as-code repo for trly.dev — a set of Docker Compose services managed by [quad-ops](https://trly.github.io/quad-ops/) to generate Podman Quadlet systemd units.

## Architecture
Each subdirectory is a self-contained service with its own `compose.yml` (and optional `.env`):
- **caddy/** — Reverse proxy (Caddy) with TLS via Porkbun DNS. Owns the `caddy_default` network.
- **kener/** — Status page (Kener) with Redis sidecar. Connects to `caddy_default`.
- **pocket-id/** — OIDC identity provider (Pocket-ID). Connects to `caddy_default`.
- **pairdrop/** — Local file sharing (PairDrop). Connects to `caddy_default`.

## Compose Conventions
- Name the primary service `app`; use `compose.yml` (not `docker-compose.yml`).
- Non-sensitive config goes in `.env` files; map vars explicitly in `environment:` with `${VAR}` interpolation.
- Secrets use `x-quad-ops-env-secrets:` (mapped to `podman secret create`), never hardcoded.
- Services needing reverse proxy access join the external `caddy_default` network.
- Multi-service stacks use a `default` network for internal communication.
- Use `restart: unless-stopped` (or `always` for support services like Redis).
- Refer to https://trly.github.io/quad-ops/docs/compose-support/ for supported/unsupported features.
