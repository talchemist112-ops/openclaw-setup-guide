# Step 3: Onboarding & Configuration

## What you'll do

Run the OpenClaw setup wizard that walks you through connecting your API key, setting up the background service (daemon), and configuring basic settings.

---

## Prerequisites

- Completed [Step 2: Install OpenClaw](02-install-openclaw.md)
- You have your API key ready (from [Step 1](01-prerequisites.md))

---

## Steps

### 3.1 — Run the onboarding wizard

```powershell
openclaw onboard --install-daemon
```

**What this does**:
- `onboard` — Launches the interactive setup wizard
- `--install-daemon` — Also installs OpenClaw as a background service that starts automatically when your computer boots

**The wizard will ask you**:

1. **Which AI provider?** — Select `Anthropic` (recommended), `OpenAI`, or `Google`
2. **API key** — Paste the API key you saved in Step 1
3. **Model** — Choose the model (e.g., `claude-sonnet-4-20250514` for a good balance of speed and quality, or `claude-opus-4-0-20250415` for best quality)

**Expected output**: The wizard completes with a success message and opens the Control UI dashboard in your browser.

> **This takes about 2 minutes.**

### 3.2 — Verify the gateway is running

The "gateway" is OpenClaw's background service that keeps everything connected.

```powershell
openclaw gateway status
```

**Expected output**:
```
Gateway listening on port 18789
```

If it's not running, start it:

```powershell
openclaw gateway --port 18789
```

### 3.3 — Open the dashboard

```powershell
openclaw dashboard
```

**What this does**: Opens the OpenClaw Control UI in your web browser. This is a local web page (at `http://localhost:18789`) where you can:
- Chat with your AI assistant directly
- See connected channels
- Monitor activity

**The dashboard will ask for a Gateway Token.** Get it by running:
```bash
# Inside Ubuntu
cat ~/.openclaw/openclaw.json | grep token
```

Paste the token value into the **Gateway Token** field and click **Connect**.

**Try sending a test message** in the chat — type "Hello, who are you?" and press Enter. If you get a response, everything is working!

> **Important on WSL1**: The gateway must be started inside **tmux** to stay alive:
> ```bash
> # Start gateway that survives terminal close
> tmux new-session -d -s openclaw 'source ~/.bashrc && openclaw gateway --port 18789'
> # Detach from tmux: Ctrl+B then D
> # Reattach later: tmux attach -t openclaw
> ```

### 3.4 — Understand the configuration file

OpenClaw stores its settings in a file called `openclaw.json`:

**Location**: `~/.openclaw/openclaw.json`

**Inside WSL**, this is at: `/home/salman/.openclaw/openclaw.json`

**From Windows**, access it via: `\\wsl$\Ubuntu\home\salman\.openclaw\openclaw.json`

Here's what a minimal config looks like:

```json5
{
  // Which AI model to use
  agent: {
    model: "anthropic/claude-sonnet-4-20250514"
  },

  // Gateway settings
  gateway: {
    mode: "local"
  },

  // Who can talk to your assistant (security)
  channels: {
    whatsapp: {
      dmPolicy: "allowlist",
      allowFrom: ["+15551234567"]  // Replace with YOUR phone number
    }
  },

  // Heartbeat (proactive mode) — keep disabled until you're comfortable
  heartbeat: {
    every: "0m"
  }
}
```

> **Don't edit this file yet** — we'll configure channels in the next step.

### 3.5 — Explore the workspace

OpenClaw creates a workspace at `~/.openclaw/workspace/` with these files:

| File | What it does |
|------|-------------|
| `SOUL.md` | Defines your assistant's personality and behavior |
| `USER.md` | Information about you (your name, preferences, etc.) |
| `TOOLS.md` | What tools/skills the assistant has access to |
| `IDENTITY.md` | The assistant's name and identity |
| `AGENTS.md` | Multi-agent routing rules (advanced) |
| `HEARTBEAT.md` | Instructions for proactive tasks (advanced) |

You can edit these later to customize your assistant's behavior. For now, the defaults work fine.

### 3.6 — Set a spending limit (Important!)

OpenClaw uses your API key to call the AI model. Each message costs a small amount. To prevent surprise bills:

**For Anthropic:**
1. Go to https://console.anthropic.com
2. Go to **Plans & Billing** > **Spend Limits**
3. Set a monthly limit (e.g., $20-50)

**For OpenAI:**
1. Go to https://platform.openai.com/settings/organization/billing
2. Set a monthly usage limit

---

## Verify success

Run these commands to confirm everything is set up:

```powershell
# Check OpenClaw status
openclaw status
```

**Expected output**: Shows your configuration, connected channels, and gateway status.

```powershell
# Full diagnostic
openclaw status --all
```

**Expected output**: Detailed report of your entire setup.

---

## What you've done

- Ran the onboarding wizard (API key, model, daemon)
- The gateway is running in the background
- You can chat with your assistant via the Control UI dashboard
- Configuration lives at `~/.openclaw/openclaw.json`
- Workspace files live at `~/.openclaw/workspace/`

---

**Next step**: [04-channel-setup.md](04-channel-setup.md) — Connect WhatsApp and/or Telegram
