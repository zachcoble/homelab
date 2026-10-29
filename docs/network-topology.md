# Network Topology

> Addresses and hostnames are redacted throughout — see the note in the main README.

## Logical design

Two trust domains:

1. **Trusted LAN** — local east-west traffic and core service reachability.
2. **Tailscale overlay** — the only path for remote administration.

Remote access is documented through Tailscale; no router port forwarding is described here.

## Remote access paths

### Proxmox
- LAN administration: SSH to the hypervisor on the trusted LAN only.
- Remote administration: Tailscale overlay only.
- Browser UI: bound to the LAN interface, not exposed publicly.

### RustDesk
Remote client → Tailscale overlay → self-hosted relay (hbbs/hbbr) → target client.

Firewall policy on the relay host:
- RustDesk service ports allowed only on the Tailscale interface
- Same ports blocked on every other interface
- No public router forwarding

### n8n
Authorized device → Tailscale overlay → reverse-proxied HTTPS endpoint → local app listener on loopback only.

### Caddy / internal services
Caddy terminates TLS using wildcard certificates issued via Cloudflare's DNS-01 challenge, then reverse-proxies to the internal services (Nextcloud, Jellyfin). DNS-01 means no challenge endpoint ever needs to be reachable from the public Internet — the whole certificate issuance flow happens through the DNS provider's API.

## Addressing notes

Tailscale addresses are CGNAT-space private overlay addresses, not ordinary public Internet endpoints — but they're still treated as sensitive here and redacted, since topology and host identity can help an attacker after some other compromise.
