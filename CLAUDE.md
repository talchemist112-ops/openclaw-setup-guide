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
- **Gateway confirmed working** — starts in ~7s, tmux keeps it alive
- **Dashboard accessible** at http://127.0.0.1:18789 from Windows browser
- tmux installed (`tmux 3.2a`) for persistent gateway sessions

### What's CONFIGURED (2026-04-13)
- **Anthropic API key** — configured in `~/.bashrc` as `ANTHROPIC_API_KEY` (DO NOT commit to git)
- **OpenAI API key** — configured in `~/.bashrc` as `OPENAI_API_KEY` (DO NOT commit to git)
- **Default model**: `anthropic/claude-sonnet-4-20250514`
- **Gateway confirmed working** with Claude Sonnet model

### What's LEFT TO DO
1. **No messaging channel connected** — User needs to pick WhatsApp/Telegram/Discord
   - WhatsApp: needs a dedicated phone number + QR scan
   - Telegram: needs a bot token from @BotFather
   - Discord: needs a bot token from Discord Developer Portal
2. **Spending limit** — User should set a monthly spend limit on Anthropic console
3. **Bundled plugin deps** — Run `wsl -d Ubuntu -u salman -- bash -c "source ~/.bashrc && openclaw doctor --fix"` for full plugin support
4. **Customize assistant** — Edit SOUL.md, USER.md in workspace to personalize behavior

### SECURITY — API Keys
- Keys are stored ONLY in `/home/salman/.bashrc` inside WSL (local to this machine)
- **NEVER** commit keys to git, CLAUDE.md, or any tracked file
- To rotate keys: edit `~/.bashrc`, update the values, restart gateway
- Anthropic console: https://console.anthropic.com (set spending limits here)
- OpenAI platform: https://platform.openai.com (set spending limits here)

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

**GUI access**: Open browser at **http://127.0.0.1:18789** while gateway is running.

## Environment Reference

### Server
- **OS**: Windows Server 2022 Standard Evaluation (build 20348)
- **Platform**: x86_64
- **User**: DENODO\salman

### WSL
- **WSL version**: 1 (WSL2 NOT supported on this build — see Known Issues)
- **Kernel**: 5.10.16
- **Distro**: Ubuntu 22.04.5 LTS (Jammy)
- **WSL user**: salman (passwordless sudo)
- **Install path**: `C:\Users\salman\WSL\Ubuntu`
- **Enter WSL**: `wsl -d Ubuntu -u salman`

### Node.js (inside WSL)
- **Node.js**: v22.22.2 (v24 DOES NOT WORK on WSL1)
- **npm**: 10.9.7
- **Global packages path**: `/usr/lib/node_modules/`

### OpenClaw (inside WSL)
- **Version**: 2026.4.11 (769908e)
- **Binary**: `/usr/bin/openclaw`
- **Config**: `/home/salman/.openclaw/openclaw.json`
- **Workspace**: `/home/salman/.openclaw/workspace/`
- **Logs**: `/tmp/openclaw-1000/openclaw-2026-04-13.log`
- **Gateway port**: 18789
- **Gateway auth token**: `949b8d7760347dfa3cd74f7f7e7ea3c52de22184cb3a6f70`
- **Dashboard URL**: http://127.0.0.1:18789
- **Default model**: `anthropic/claude-sonnet-4-20250514` (user-configured)
- **Fallback model**: OpenAI key also configured — can switch with `openclaw config set agents.defaults.model openai/gpt-4o`
- **API keys location**: `~/.bashrc` inside WSL (NEVER in git-tracked files)

### Networking
- Gateway binds to `127.0.0.1:18789` (loopback only — accessible from Windows browser, NOT from external machines)
- WSL1 shares the Windows network stack — ports opened in WSL are accessible on Windows localhost
- Browser control server on port `18791`
- MCP loopback on a random high port (changes each restart)

### Process Management
- **tmux 3.2a** installed for persistent sessions
- Start gateway: `tmux new -s openclaw 'openclaw gateway --port 18789'`
- Detach: `Ctrl+B` then `D`
- Reattach: `wsl -d Ubuntu -u salman -- bash -c "tmux attach -t openclaw"`
- Check session: `wsl -d Ubuntu -u salman -- bash -c "tmux has-session -t openclaw && echo ALIVE || echo DEAD"`
- **WSL1 limitation**: all processes die when last shell exits — MUST use tmux or keep a terminal open

## API Key Setup

**IMPORTANT**: A ChatGPT Plus subscription ($20/mo) is NOT an API key. They are separate products. OpenClaw needs an API key, not a chat subscription.

### Option A: OpenAI API (separate from ChatGPT subscription)
1. Go to https://platform.openai.com
2. Sign up (can use same login as ChatGPT)
3. Go to **Billing** > add credit ($5 minimum top-up)
4. Go to **API Keys** > **Create new secret key**
5. Configure in WSL:
```bash
wsl -d Ubuntu -u salman
openclaw config set agents.defaults.model openai/gpt-4o
echo 'export OPENAI_API_KEY=sk-your-key-here' >> ~/.bashrc
source ~/.bashrc
# Restart gateway in tmux
tmux kill-session -t openclaw 2>/dev/null
tmux new -d -s openclaw 'openclaw gateway --port 18789'
```

### Option B: Anthropic Claude API (recommended quality)
1. Go to https://console.anthropic.com
2. Sign up, go to **Billing** > add $5-10 credit
3. Go to **API Keys** > **Create Key**
4. Configure:
```bash
wsl -d Ubuntu -u salman
openclaw config set agents.defaults.model anthropic/claude-sonnet-4-20250514
echo 'export ANTHROPIC_API_KEY=sk-ant-your-key-here' >> ~/.bashrc
source ~/.bashrc
tmux kill-session -t openclaw 2>/dev/null
tmux new -d -s openclaw 'openclaw gateway --port 18789'
```

### Option C: Google Gemini (cheapest / free tier available)
1. Go to https://aistudio.google.com/apikey
2. Click **Create API Key** (free tier = 60 requests/min)
3. Configure:
```bash
wsl -d Ubuntu -u salman
openclaw config set agents.defaults.model google/gemini-2.5-flash
echo 'export GEMINI_API_KEY=your-key-here' >> ~/.bashrc
source ~/.bashrc
tmux kill-session -t openclaw 2>/dev/null
tmux new -d -s openclaw 'openclaw gateway --port 18789'
```

### Critical: Create the auth store file
Setting the env var in `~/.bashrc` is NOT enough. You MUST also create the auth-profiles.json:
```bash
cat > ~/.openclaw/agents/main/agent/auth-profiles.json << 'EOF'
{
  "anthropic": { "apiKey": "YOUR-ANTHROPIC-KEY" },
  "openai": { "apiKey": "YOUR-OPENAI-KEY" }
}
EOF
chmod 600 ~/.openclaw/agents/main/agent/auth-profiles.json
```
Then restart the gateway.

### After setting any API key
1. Restart gateway: `tmux kill-session -t openclaw; tmux new-session -d -s openclaw 'source ~/.bashrc && openclaw gateway --port 18789'`
2. Open browser at **http://127.0.0.1:18789**
3. Paste gateway token and click **Connect**
4. Type "Hello" — if you get a response, it's working

### Cost comparison
| Provider | Model | Quality | Cost per 1M tokens |
|----------|-------|---------|-------------------|
| Anthropic | claude-sonnet-4 | Best | ~$3 in / $15 out |
| OpenAI | gpt-4o | Great | ~$2.50 in / $10 out |
| Google | gemini-2.5-flash | Good | ~$0.15 in / $0.60 out |
| Google | gemini-2.5-pro | Great | ~$1.25 in / $10 out |

Casual daily use ≈ $0.50-$5/day depending on model and usage.

## Known Issues & Solutions

### Issue: WSL `--install -d Ubuntu` fails with 0x80072ee7
- **Symptom**: `wsl --install -d Ubuntu` returns error code 0x80072ee7
- **Root cause**: DNS/network issue on Windows Server 2022 blocking Microsoft Store downloads
- **Solution**: Manually download rootfs and import:
  ```powershell
  curl -L -o C:\Users\salman\Downloads\ubuntu-rootfs.tar.gz "https://cloud-images.ubuntu.com/wsl/jammy/current/ubuntu-jammy-wsl-amd64-ubuntu22.04lts.rootfs.tar.gz"
  powershell.exe -Command "& { wsl.exe --import Ubuntu C:\Users\salman\WSL\Ubuntu C:\Users\salman\Downloads\ubuntu-rootfs.tar.gz }"
  ```
- **Don't try**: `wsl --install --web-download` — flag not supported on this build

### Issue: WSL2 commands silently fail (print help instead of executing)
- **Symptom**: `wsl --set-default-version 2`, `wsl --set-version Ubuntu 2`, `wsl --import ... --version 2` all just print help text
- **Root cause**: Bug in WSL build shipped with Windows Server 2022 (build 20348). Argument parsing is broken for multi-parameter commands.
- **Solution**: Accept WSL1. It works fine for Node.js/OpenClaw. Only `--import` (without `--version`), `--unregister`, `--list`, `--status`, `--shutdown` work reliably.
- **Don't try**: Running through PowerShell scripts, batch files, or `& { }` wrappers — none fix it. The bug is in wsl.exe itself.

### Issue: Node.js v24 "Exec format error" on WSL1
- **Symptom**: `node --version` returns "cannot execute binary file: Exec format error"
- **Root cause**: Node.js v24 binaries use syscalls/features not supported by WSL1's translation layer
- **Solution**: Use Node.js v22 instead: `curl -fsSL https://deb.nodesource.com/setup_22.x | sudo bash - && sudo apt install -y nodejs`
- **Don't try**: v24.x on WSL1 — it will never work without WSL2

### Issue: WSL2 kernel MSI won't install silently
- **Symptom**: `msiexec /i wsl_update_x64.msi /quiet` returns exit code 103
- **Root cause**: MSI needs admin elevation
- **Solution**: `powershell.exe -Command "Start-Process msiexec.exe -ArgumentList '/i','C:\Users\salman\Downloads\wsl_update_x64.msi','/quiet','/norestart' -Verb RunAs -Wait"`

### Issue: Gateway process dies when WSL shell exits
- **Symptom**: `openclaw gateway` starts fine (reaches "ready" in ~7s) but process disappears when terminal closes
- **Root cause**: WSL1 does NOT keep processes running after the last shell exits (no background VM like WSL2)
- **Solution**: Keep a terminal open with the gateway running. Two approaches:
  1. **Simple**: Open a PowerShell window, run `wsl -d Ubuntu -u salman`, then `openclaw gateway --port 18789`. Leave window open.
  2. **Better**: Install tmux (`sudo apt install -y tmux`), run `tmux new -s openclaw`, start gateway inside tmux, detach with `Ctrl+B` then `D`. Reattach with `tmux attach -t openclaw`.
- **Don't try**: `nohup ... &`, `disown`, or background `&` — WSL1 kills all child processes when the parent shell exits regardless.

### Issue: "No API key found for provider" even though env var is set
- **Symptom**: Dashboard shows `No API key found for provider "anthropic". Auth store: ~/.openclaw/agents/main/agent/auth-profiles.json`
- **Root cause**: OpenClaw looks for API keys in its **auth store file**, not just environment variables. The `~/.bashrc` env var alone is not enough.
- **Solution**: Create the auth-profiles.json file manually:
  ```bash
  cat > ~/.openclaw/agents/main/agent/auth-profiles.json << 'EOF'
  {
    "anthropic": {
      "apiKey": "sk-ant-YOUR-KEY-HERE"
    },
    "openai": {
      "apiKey": "sk-proj-YOUR-KEY-HERE"
    }
  }
  EOF
  chmod 600 ~/.openclaw/agents/main/agent/auth-profiles.json
  ```
  Then restart the gateway.
- **Don't try**: Only setting `ANTHROPIC_API_KEY` in `~/.bashrc` — the env var alone doesn't populate the auth store. You need both (env var for CLI, auth-profiles.json for the agent runtime).
- **Don't try**: `openclaw configure --section model` with piped input — the interactive wizard doesn't accept piped/automated input.

### Issue: tmux session doesn't inherit environment variables
- **Symptom**: Gateway starts but uses wrong model or "no API key" errors
- **Root cause**: tmux creates a new shell that doesn't inherit exported env vars from the parent
- **Solution**: Source ~/.bashrc inside the tmux command:
  ```bash
  tmux new-session -d -s openclaw 'source ~/.bashrc && openclaw gateway --port 18789'
  ```
- **Don't try**: Exporting vars before `tmux new-session` — tmux forks a new shell and loses them

### Issue: Imported WSL distro runs as root by default
- **Symptom**: All commands run as root, no regular user exists
- **Root cause**: Manually imported rootfs via `wsl --import` doesn't create a user or set defaults
- **Solution**:
  ```bash
  # As root inside WSL:
  useradd -m -s /bin/bash salman
  echo 'salman ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
  echo -e '[user]\ndefault=salman' > /etc/wsl.conf
  # Then from PowerShell: wsl --shutdown
  # Reopen: wsl -d Ubuntu  (now logs in as salman)
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
