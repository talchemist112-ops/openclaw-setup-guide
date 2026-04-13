# CLAUDE.md — OpenClaw.ai Setup Guide

## Project Purpose

This repository contains a **complete, zero-to-hero setup guide** for **OpenClaw.ai** — a free, open-source personal AI assistant that runs locally on your machine and connects to WhatsApp, Telegram, Discord, and more.

OpenClaw can clear your inbox, send emails, manage your calendar, check you in for flights, browse the web, run scripts, and automate workflows — all through chat messages.

## Current Deployment State (as of 2026-04-13)

OpenClaw is **partially deployed** on this server. Here's what's done and what's left:

### What's DONE
- WSL kernel v5.10.16 installed on Windows Server 2022
- Ubuntu 22.04.5 LTS imported into WSL (version 1, NOT WSL2 — see notes below)
- Ubuntu user: `salman` (passwordless sudo, default WSL user via /etc/wsl.conf)
- Ubuntu install location: `C:\Users\salman\WSL\Ubuntu`
- Node.js v22.22.2 + npm 10.9.7 installed globally in Ubuntu
- OpenClaw 2026.4.11 (769908e) installed globally via `npm install -g openclaw@latest`
- Gateway mode set to `local`
- Auth token generated (token-based auth enabled)
- Workspace created at `/home/salman/.openclaw/workspace/`
- Workspace files: AGENTS.md, SOUL.md, TOOLS.md, IDENTITY.md, USER.md, HEARTBEAT.md, BOOTSTRAP.md
- Config file: `/home/salman/.openclaw/openclaw.json`
- Permissions fixed (chmod 700 on ~/.openclaw)

### What's LEFT TO DO (needs user input)
1. **API key not configured** — User must provide an Anthropic/OpenAI/Google API key
   - Run: `wsl -d Ubuntu -u salman -- bash -c "openclaw configure --section model"`
   - Or set directly: `wsl -d Ubuntu -u salman -- bash -c "openclaw config set agents.defaults.model anthropic/claude-sonnet-4-20250514"`
   - Then set the key: `wsl -d Ubuntu -u salman -- bash -c "ANTHROPIC_API_KEY=sk-ant-xxx openclaw gateway"`
2. **No messaging channel connected** — User needs to pick WhatsApp/Telegram/Discord
   - WhatsApp: needs a dedicated phone number + QR scan
   - Telegram: needs a bot token from @BotFather
   - Discord: needs a bot token from Discord Developer Portal
3. **Spending limit** — User must set a monthly spend limit on their API provider
4. **Bundled plugin deps** — Run `wsl -d Ubuntu -u salman -- bash -c "openclaw doctor --fix"` to install missing plugin dependencies (WhatsApp Baileys lib, etc.)
5. **Gateway not running** — Start with: `wsl -d Ubuntu -u salman -- bash -c "openclaw gateway --port 18789"`

### Important Technical Notes
- **WSL version is 1, not 2** — This server's WSL build (Windows Server 2022 build 20348) has a bug where `--set-default-version 2`, `--set-version`, and `--import --version 2` all fail silently. WSL1 works fine for Node.js/OpenClaw but does NOT support systemd. The gateway must be started manually (not as a daemon).
- **Node.js v24 does NOT work on WSL1** — Gives "Exec format error". Must use Node.js v22.
- **`wsl --install -d Ubuntu`** fails with error 0x80072ee7 (network/DNS issue). Workaround: manually download rootfs from `https://cloud-images.ubuntu.com/wsl/jammy/current/` and import with `wsl --import`.
- **WSL argument passing** — Running `wsl --set-version` or `wsl --set-default-version` from bash or PowerShell just prints help text on this build. Only `wsl --import`, `wsl --unregister`, `wsl --list`, `wsl --status`, and `wsl --shutdown` work reliably.

## How to Access OpenClaw

```powershell
# Enter Ubuntu
wsl -d Ubuntu -u salman

# Inside Ubuntu, check status
openclaw --version
openclaw status
openclaw doctor

# Start the gateway (must be done each time, no systemd on WSL1)
openclaw gateway --port 18789

# Open dashboard (from Windows browser: http://localhost:18789)
openclaw dashboard
```

## Key Details

- **OpenClaw website**: https://openclaw.ai
- **OpenClaw GitHub**: https://github.com/openclaw/openclaw
- **Official docs**: https://docs.openclaw.ai
- **License**: MIT (free and open source)
- **Installed platform**: WSL1 / Ubuntu 22.04 on Windows Server 2022

## Project Structure

```
openclaw-setup-guide/
├── CLAUDE.md                    # This file — project state + instructions for Claude
├── README.md                    # Overview and quick links
├── 01-prerequisites.md          # Step 1: Node.js, API key, phone number
├── 02-install-openclaw.md       # Step 2: Install OpenClaw on your machine
├── 03-onboarding-config.md      # Step 3: Run onboard wizard, configure settings
├── 04-channel-setup.md          # Step 4: Connect WhatsApp / Telegram
├── 05-daily-usage.md            # Step 5: How to use OpenClaw day-to-day
├── 06-troubleshooting.md        # Common errors and fixes
```

## Writing Rules

1. Every guide file must start with a clear **What you'll do** section
2. Every command must have a one-line explanation above it
3. Use numbered steps — never skip a step
4. Include expected output after commands so the reader can verify success
5. Mark optional steps clearly with "(Optional)"
6. Cross-reference other guide files when needed
7. Keep language simple — no jargon without explanation
8. Show both Windows (PowerShell) and WSL/Linux commands where they differ
