# homelab-scripts

Collection of shell scripts for homelab monitoring, backup automation, and infrastructure management. Battle-tested on a 62 CT + 1 VM, 4-node Proxmox cluster.

## Scripts

### Monitoring

| Script | Description |
|---|---|
| [`cert-check`](cert-check) | TLS certificate expiration checker — SNI-aware, configurable warning thresholds, supports services behind reverse proxy |
| [`http-check`](http-check) | HTTP availability checker for multiple services — bulk check with OK/WARN/FAIL status |
| [`pve-status`](pve-status) | Proxmox VE cluster status — nodes, CTs, CPU/RAM usage via API |
| [`loki-query`](loki-query) | Loki log aggregation wrapper — query logs by service with pre-configured filters |

### Backup

Backup orchestration moved out of this collection: **[pbs-autobackup](https://github.com/ferr079/pbs-autobackup)**.

It runs the same cycle — Wake-on-LAN the backup host, `vzdump` every node, prune, garbage-collect, shut down — but as a maintained standalone tool: documented environment file, `--dry-run`, per-node exit codes, optional Telegram wrapper, ShellCheck CI. Keeping a second copy here only meant two places to fix the same bug.

## Usage

```bash
# Check all TLS certificates, warn if expiring in <30 days
./cert-check all --warn 30

# Check HTTP availability of all services
./http-check all

# Proxmox cluster overview
./pve-status all

# Query Traefik error logs from the last hour
./loki-query traefik 1h errors
```

## Requirements

- Bash 4+
- `curl`, `openssl` (cert-check)
- Proxmox API token, `PVEAuditor` is enough (pve-status)
- Loki instance (loki-query)

## Configuration

Nothing is hardcoded in the scripts — hosts, URLs and tokens all come from two files under `~/.config/homelab-scripts/`:

```bash
mkdir -p ~/.config/homelab-scripts
cp examples/services.conf.example        ~/.config/homelab-scripts/services.conf
cp examples/homelab-scripts.env.example  ~/.config/homelab-scripts/homelab-scripts.env
chmod 600 ~/.config/homelab-scripts/homelab-scripts.env
```

| File | Read by | Contents |
|---|---|---|
| `services.conf` | `http-check`, `cert-check` | one `name\|url` per line; `cert-check` uses the `https://` ones, hostname as SNI |
| `homelab-scripts.env` | `pve-status`, `loki-query` | `PVE_NODES`, `LOKI_URL`, thresholds |

Both paths can be overridden: `SERVICES_FILE`, `HOMELAB_SCRIPTS_ENV`, `PVE_ENV`. Every value can also be exported directly in the environment.

**Proxmox tokens belong in that env file and nowhere else.** Give them the smallest role that works — `pve-status` only reads, so `PVEAuditor` is enough.

## License

MIT
