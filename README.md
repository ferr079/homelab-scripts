# homelab-scripts

Collection of shell scripts for homelab monitoring, backup automation, and infrastructure management. Battle-tested on a 55+ CT/VM 4-node Proxmox cluster.

## Scripts

### Monitoring

| Script | Description |
|---|---|
| [`cert-check`](cert-check) | TLS certificate expiration checker — SNI-aware, configurable warning thresholds, supports services behind reverse proxy |
| [`http-check`](http-check) | HTTP availability checker for multiple services — bulk check with OK/WARN/FAIL status |
| [`pve-status`](pve-status) | Proxmox VE cluster status — nodes, CTs, CPU/RAM usage via API |
| [`loki-query`](loki-query) | Loki log aggregation wrapper — query logs by service with pre-configured filters |

### Backup

| Script | Description |
|---|---|
| [`pbs-backup`](pbs-backup) | Proxmox Backup Server automation — WOL remote node, vzdump all CTs, prune, garbage collect, shutdown. Full unattended backup cycle. |

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

# Full PBS backup cycle (WOL → backup → prune → GC → shutdown)
./pbs-backup --dry-run
```

## Requirements

- Bash 4+
- `curl`, `openssl` (cert-check)
- Proxmox API token (pve-status, pbs-backup)
- SSH key access to target nodes (pbs-backup)
- Loki instance (loki-query)

## Configuration

Each script uses environment variables or inline configuration arrays. Edit the `SERVICES`, `SERVICE_URLS`, or API endpoints at the top of each script to match your infrastructure.

## License

MIT
