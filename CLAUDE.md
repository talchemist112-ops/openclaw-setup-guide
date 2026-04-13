# CLAUDE.md — OpenClaw.ai Setup Guide

## Project Purpose

This repository contains a **complete, zero-to-hero setup guide** for **OpenClaw.ai** — a free, open-source personal AI assistant that runs locally on your machine and connects to WhatsApp, Telegram, Discord, and more.

OpenClaw can clear your inbox, send emails, manage your calendar, check you in for flights, browse the web, run scripts, and automate workflows — all through chat messages.

## Key Details

- **OpenClaw website**: https://openclaw.ai
- **OpenClaw GitHub**: https://github.com/openclaw/openclaw
- **Official docs**: https://docs.openclaw.ai
- **License**: MIT (free and open source)
- **Target platform**: Windows (PowerShell) or WSL2/Ubuntu — guide covers both
- **Audience**: Complete beginners — every command must be explained

## Project Structure

```
openclaw-setup-guide/
├── CLAUDE.md                    # This file — project instructions for Claude
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
