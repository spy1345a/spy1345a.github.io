# 🎮 Public Minecraft SMP Server

> A publicly accessible Minecraft survival multiplayer server, containerised and hosted on self-hosted hardware, serving a real player community.

---

## Overview

A production Minecraft server running as a Docker container on self-hosted hardware, open to the public. Managing a live game server with real users means real uptime pressure — players notice when it goes down.

**Status:** 🟢 Live  
**Hosting:** Self-hosted (home lab)  
**Container:** Docker  
**Access:** Public internet

---

## Stack

```
Players (Internet)
      │
      ▼
Self-hosted Server (Home Lab)
      │
      ▼
Docker Container (Minecraft Java Server)
      ├── Persistent world data (named volume)
      ├── Plugin directory (bind mount)
      ├── Server config (server.properties)
      └── Restart policy: unless-stopped
```

---

## Operations

### Uptime Management
- Container set to auto-restart on crash or reboot
- VPS has automatic boot — server recovers from cloud provider maintenance automatically
- World backups run on schedule via cron

### Updates & Maintenance
- Plugin updates tested on a local copy before applying to live server
- Server version upgrades done during low-player windows
- Announcement to players via in-game broadcast before restarts

### Player Management
- Whitelist / ban management via server console
- OP permissions managed carefully — principle of least privilege applies here too

---

## What Running a Public Server Teaches You

This isn't a local LAN server. It's a public service with real users who expect it to be available:

- **Incident response** — when the server crashes at 2am, you fix it
- **Capacity planning** — too many players = lag = complaints
- **Update management** — plugins break on version upgrades, you learn to test first
- **Security** — public Minecraft servers get probed and griefed; you learn to harden
- **Communication** — players need to know about downtime in advance

These are the same fundamentals as running any production service.

---

## Key Skills Demonstrated

- Docker container lifecycle management
- Persistent data management with volumes
- Cron-based backup automation
- Public-facing service security (firewall, rate limiting)
- Incident response and service recovery
- User-facing operations (communication, scheduling maintenance)

---

*Part of [spy1345a's portfolio](../README.md)*
