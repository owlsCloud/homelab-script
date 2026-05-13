# homelab-script

A collection of Bash scripts for managing and monitoring my self-hosted homelab — a headless Debian NAS running Docker-based services, accessed remotely via Tailscale.

## Scripts

### `nas_check`

Runs automatically on SSH login. Gives an at-a-glance health check of the system without having to run anything manually.

**Checks:**
- NAS mount point status (`/mnt/nas`)
- System uptime and last boot time
- Systemd services: `tailscaled`, `smbd`, `nmbd`, `docker`, `pihole-FTL`, `lighttpd`
- Docker containers: `jellyfin`, `immich_server`, `immich_microservices`, `immich_postgres`, `immich_redis`
- Docker restart policies (warns if not set to `always` or `unless-stopped`)

**Output:**
- `[OK]` in green for healthy services
- `[WARN]` in yellow for non-critical issues
- `[DOWN]` in red for critical failures
- Exits with code `1` if any critical component is down

**Setup:**
```bash
mkdir -p ~/bin
cp nas_check ~/bin/nas_check
chmod +x ~/bin/nas_check
```

Then add the following to your `~/.bashrc` or `~/.bash_profile`:
```bash
if [[ -n "${SSH_CONNECTION:-}" ]]; then
    "$HOME/bin/nas_check" || true
    echo
fi
```

## Stack

| Service | Purpose |
|---|---|
| Jellyfin | Media server |
| Immich | Photo backup and management |
| Samba | Network file sharing |
| Pi-hole | Network-wide ad and DNS blocking |
| Tailscale | Secure remote access (no open ports) |

## Environment

- **OS:** Debian (headless)
- **Access:** Tailscale VPN
- **Container runtime:** Docker
