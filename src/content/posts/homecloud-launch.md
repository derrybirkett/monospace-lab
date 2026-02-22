---
title: "Building HomeCloud: From Hobby Setup to Self-Healing Private Cloud in One Night"
description: "How I turned a Mac mini in Barcelona into a self-healing private cloud with Tailscale, Docker, and infrastructure-as-code."
date: 2026-02-21
---

I've been running a Mac mini in Barcelona as a remote server for a while. Parsec for remote desktop, Sunshine for streaming, Docker for services, Tailscale for secure access. It worked — until it didn't.

A router reboot knocked Tailscale offline. No Tailscale meant no VNC, no Parsec, no Sunshine, no Open WebUI. Everything gone. The only fix was physical access to the machine.

That incident made something clear: I wasn't running infrastructure. I was running a desktop that happened to be on.

So I built HomeCloud.

---

## What is HomeCloud?

HomeCloud is a private, self-hosted infrastructure project. One Mac mini. No public ports. No cloud subscriptions. Everything runs through a Tailscale encrypted overlay network and is managed as infrastructure-as-code from a single Git repository.

The design principle is simple: **the Mac mini is treated as a server that happens to have a GUI.**

---

## What Changed

### The Problem

The App Store version of Tailscale uses a macOS NetworkExtension — essentially a VPN plugin that depends on the GUI session. When the router rebooted, the NetworkExtension didn't recover. Every service behind Tailscale became unreachable.

### The Fix

I replaced the App Store Tailscale with the standalone `tailscaled` daemon installed via Homebrew. It runs as root through `launchd`, independent of any GUI session. It starts on boot. It reconnects automatically. No user login required.

That single change turned a fragile setup into a resilient one.

---

## The Stack

Everything DaMini (the Mac mini) runs:

- **Tailscale** — standalone daemon, encrypted overlay network, zero public ports
- **Docker Desktop** — container runtime with restart policies
- **Open WebUI** — local AI chat interface (Docker container)
- **Ollama** — local LLM runtime (native via Homebrew)
- **Uptime Kuma** — uptime monitoring dashboard (Docker container)
- **Sunshine** — low-latency desktop streaming (login item + LaunchAgent)
- **Parsec** — remote desktop (login item)

All services auto-start. All containers have `restart: unless-stopped` or `restart: always`. Sunshine and Tailscale have watchdog plists that check every 60–120 seconds.

---

## Infrastructure as Code

The HomeCloud repo isn't documentation — it's a control plane.

```
homecloud/
├── compose/          # Docker Compose service definitions
├── scripts/          # Operational scripts
├── launchd/          # macOS service persistence (plists)
├── configs/          # Configuration documentation
├── inventory/        # Host inventory and secrets templates
└── Makefile          # Control plane entry point
```

Every operational action has a `make` target:

```
make up              # Start services
make down            # Stop services
make health          # Run 13-point healthcheck
make health-alert    # Healthcheck + Telegram alert on failure
make alert-test      # Test Telegram notification
make bootstrap       # Initial server setup
make backup          # Backup configs
```

Secrets are never committed. Environment variables are loaded from `~/.secrets` (shell-level) with a fallback to a project-level `.env` file for launchd jobs.

---

## The Healthcheck

The healthcheck script runs 13 checks across 6 categories:

- **Network** — internet reachable, DNS resolving
- **Tailscale** — daemon running, connected to tailnet
- **Docker** — daemon running, open-webui container, uptime-kuma container
- **Ollama** — process running
- **Services** — Sunshine running, Screen Sharing enabled
- **Disk** — usage for System, HomeCloud_Data, and LaCie volumes (warns at 85%)

Results are logged with timestamps. Failures trigger a Telegram alert with the specific checks that failed.

---

## The Reboot Test

The real proof: I rebooted DaMini intentionally and watched from my MacBook.

**12 out of 13 checks passed with zero manual intervention.**

Tailscale reconnected. Docker restarted all containers. Ollama came back. Sunshine resumed streaming. The only failure was Screen Sharing, which isn't enabled — and that's expected.

Recovery time: automatic. No SSH. No physical access. No panic.

---

## What's Next

The roadmap has six phases. Phase 1 (stabilisation) and Phase 2 (monitoring) are complete. Up next:

- **Phase 3** — Hardware resilience: UPS, smart plug for remote power cycling
- **Phase 4** — Networking: Pi-hole for DNS filtering, Tailscale exit node
- **Phase 5** — Infrastructure as code: Docker Compose files, watchdog scripts, config backups all versioned
- **Phase 6** — Migration: evaluate Unraid/Proxmox, containerise everything, cluster expansion

---

## The Takeaway

You don't need a rack. You don't need Proxmox. You don't need Kubernetes.

A Mac mini, Tailscale, Docker, and a well-structured Git repo got me from "hobby setup" to "self-healing private cloud" in one session. The key was treating it as infrastructure from the start — documenting decisions, scripting operations, and automating recovery.

HomeCloud is now a system, not a hobby.
