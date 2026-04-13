# OpenClaw.ai Setup Guide

A complete, step-by-step guide to install and run **OpenClaw.ai** — a free, open-source personal AI assistant that works through WhatsApp, Telegram, Discord, and more.

This guide is written so that **anyone with zero technical knowledge** can follow every step.

## What is OpenClaw?

OpenClaw is an AI assistant that runs **on your own computer** (not in the cloud). You chat with it through WhatsApp, Telegram, or other messaging apps, and it can:

- Read and clear your email inbox
- Send emails on your behalf
- Manage your Google Calendar
- Check you in for flights
- Browse the web and fill forms
- Run scripts and automate workflows
- Control smart home devices
- Remember your preferences over time

It's like having a personal secretary — but it's free, open-source, and your data stays on your machine.

## Guide Structure

Follow these guides **in order**:

| Step | Guide | What You'll Do |
|------|-------|----------------|
| 1 | [Prerequisites](01-prerequisites.md) | Get Node.js, an API key, and a phone number ready |
| 2 | [Install OpenClaw](02-install-openclaw.md) | Install OpenClaw on your machine |
| 3 | [Onboarding & Config](03-onboarding-config.md) | Run the setup wizard and configure settings |
| 4 | [Channel Setup](04-channel-setup.md) | Connect WhatsApp and/or Telegram |
| 5 | [Daily Usage](05-daily-usage.md) | How to use OpenClaw day-to-day |
| 6 | [Troubleshooting](06-troubleshooting.md) | Fix common errors |

## Requirements

- **Windows 10/11** or **Windows Server 2022** or **macOS** or **Linux**
- **Node.js v22.14+** (v24 recommended)
- **An LLM API key** — from Anthropic (Claude), OpenAI, or Google (costs ~$0.01-$5 per day depending on usage)
- **A phone number** — for WhatsApp connection (a cheap prepaid SIM works)
- Internet connection

## Quick Reference

| Resource | Link |
|----------|------|
| OpenClaw Website | https://openclaw.ai |
| GitHub Repository | https://github.com/openclaw/openclaw |
| Official Documentation | https://docs.openclaw.ai |
| Community Discord | https://discord.gg/clawd |
| Skill Hub (plugins) | https://clawhub.ai |

## Current Deployment Status (2026-04-13)

Deployed on **Windows Server 2022** with WSL1 + Ubuntu 22.04:

| Component | Status | Details |
|-----------|--------|---------|
| WSL + Ubuntu | Done | WSL1, Ubuntu 22.04.5 LTS, user `salman` |
| Node.js | Done | v22.22.2 (v24 incompatible with WSL1) |
| OpenClaw | Done | v2026.4.11, gateway mode=local |
| API Keys | Done | Anthropic (Claude) + OpenAI keys configured |
| Default Model | Done | `anthropic/claude-sonnet-4-20250514` |
| Gateway | Done | Running via tmux, dashboard at http://127.0.0.1:18789 |
| Channel (WhatsApp/Telegram) | **NOT DONE** | Need user to choose + configure |
| Spending Limits | **NOT DONE** | Set limits on console.anthropic.com |

### Quick Start (after initial setup is done)
```bash
# 1. Enter Ubuntu
wsl -d Ubuntu -u salman

# 2. Start gateway in tmux
tmux new-session -d -s openclaw 'source ~/.bashrc && openclaw gateway --port 18789'

# 3. Open browser at http://127.0.0.1:18789
# 4. Paste gateway token and click Connect
# 5. Chat!
```

**To continue setup** (channels, spending limits): see [04-channel-setup.md](04-channel-setup.md).

## Important Warnings

1. **API costs** — OpenClaw calls an LLM API (like Claude or GPT). Set spending limits on your API provider to avoid surprise bills.
2. **Security** — You're giving an AI access to your machine. Always restrict who can message it using `allowFrom` settings.
3. **Separate phone number** — Use a dedicated number for the WhatsApp connection, not your personal one.

---

*This guide was created to help anyone set up OpenClaw.ai from scratch, even with zero technical knowledge.*
