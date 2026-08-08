# Heimdall replacement research

## Recommendation

Replace Heimdall with **[Homarr](https://homarr.dev/)** and serve it at Caddy's URL root (`/`).

The original recommendation was Homepage because its YAML configuration is well suited to Git. The operational requirement was subsequently clarified: links, integrations, and layout must be editable from the dashboard itself and must not be tracked by Git. Under that requirement, Homarr is the better fit because its dashboard editor persists state in `/appdata`.

Homarr provides documented [integrations](https://homarr.dev/docs/integrations/) for media-oriented services and an official [Docker Compose deployment](https://homarr.dev/docs/getting-started/installation/docker/). Its app data needs a stable `SECRET_ENCRYPTION_KEY` and should be backed up.

## Candidate comparison

| Candidate | Configuration model | Fit |
|---|---|---|
| **Homarr** | Dashboard UI with persistent application data | **Recommended for UI-managed configuration** |
| [Homepage](https://gethomepage.dev/) | Mounted YAML files | Better when configuration should be committed |
| [Dashy](https://dashy.to/docs/) | Primarily YAML configuration | Does not meet the UI-management requirement as well |

## Routing consideration

The former directive `reverse_proxy / heimdall:80` matched only the exact `/` path, leaving dashboard assets unproxied. Caddy therefore uses a final catch-all `handle` for Homarr after the specific media-service routes. See Caddy's [`reverse_proxy` documentation](https://caddyserver.com/docs/caddyfile/directives/reverse_proxy).

## Migration outline

1. Run Homarr from a pinned image and mount `./conf/homarr:/appdata`.
2. Keep `conf/homarr/` excluded from Git, but include it in local backups.
3. Generate a 64-character hex encryption key with `openssl rand -hex 32`, store it in `.env`, and do not rotate it casually.
4. Route Caddy's final fallback to `homarr:7575`.
5. Configure Sonarr, Radarr, Prowlarr, Jellyfin, qBittorrent, and Seerr from Homarr's dashboard editor.
6. Retain `conf/heimdall/` until the new dashboard has been verified and rollback is no longer needed.

Docker socket access is intentionally omitted. It can be added later for Docker-specific integration, but ordinary application links and API integrations do not justify exposing the host Docker socket.
