# 🖥️ VPS Infrastructure

> Oracle Cloud hosted infrastructure running multiple services with container isolation and private networking.

---

## Overview

A self-managed Linux server environment hosted on Oracle Cloud Free Tier, running several public and private services with full network isolation via WireGuard VPN.

**Uptime:** 1+ year continuous operation  
**Provider:** Oracle Cloud Infrastructure (OCI)  
**OS:** Ubuntu Server / Oracle Linux  
**Shell:** Fish

---

## Architecture

```
Internet
   │
   ▼
Oracle Cloud VPS (public IP)
   ├── WireGuard VPN (UDP 51820)
   │     └── Private mesh network for secure access
   ├── Mumble Server (via Podman)
   │     └── Voice comms for SMP players
   ├── Minecraft SMP (via Docker)
   │     └── See: minecraft-smp.md
   └── Firewall (iptables)
         └── Allowlist-only ingress
```

---

## Services

### WireGuard VPN
- Private mesh network between devices
- All admin access tunnelled through VPN — no SSH exposed to public internet
- Peer configs managed manually, rotated periodically

### Mumble (Voice Server)
- Running in a **Podman** container (rootless)
- Persistent config via bind mount
- Used alongside the Minecraft SMP for player voice comms

### Container Strategy
- **Docker** for public-facing services (Minecraft)
- **Podman** for system-level services (rootless, no daemon, more secure)
- All containers use named volumes for persistent data
- Restart policies set to `unless-stopped`

---

## Networking

- Inbound: Only WireGuard port + game ports exposed
- All management done via WireGuard tunnel
- iptables rules to drop everything not explicitly allowed

---

## Key Skills Demonstrated

- Cloud instance provisioning and management (OCI console + CLI)
- Linux networking: routing, NAT, iptables
- WireGuard VPN setup from scratch (key generation, peer config, routing)
- Container orchestration with Docker and Podman
- Service hardening and minimal attack surface design

---

## Lessons Learned

- Oracle Cloud's free tier has quirky networking — VNIC secondary IPs need manual iptables rules
- Podman rootless containers handle volume permissions differently than Docker — learned this the hard way with Mumble config files
- WireGuard is remarkably simple once you understand the key exchange model; the hardest part is getting the routing table right

---

*Part of [spy1345a's portfolio](../README.md)*
