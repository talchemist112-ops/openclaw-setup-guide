# Step 1: Prerequisites

## What you'll do

Get three things ready before installing OpenClaw:
1. **Node.js** — the runtime that OpenClaw runs on
2. **An LLM API key** — so OpenClaw can use an AI model (like Claude or GPT)
3. **A phone number** — for connecting to WhatsApp (optional if using Telegram/Discord only)

---

## 1.1 — Install Node.js

Node.js is a program that lets you run JavaScript applications. OpenClaw is built with it.

**Required version**: Node.js **v22.14 or newer** (v24 recommended, but see note below)

> **WSL1 Warning**: If running on WSL version 1 (common on Windows Server 2022), Node.js v24 gives "Exec format error". Use **v22** instead. v22.22.2 is confirmed working.

### On Windows (PowerShell)

Open **PowerShell** (press Windows key, type `PowerShell`, click on it) and run:

```powershell
# Check if Node.js is already installed
node --version
```

**If you see** `v22.x.x` or `v24.x.x` — you're good, skip to section 1.2.

**If you see an error** or a version below v22.14, install Node.js:

1. Go to https://nodejs.org
2. Download the **LTS** version (or v24 if available)
3. Run the installer — click **Next** through all steps, keep defaults
4. **Important**: Check the box that says **"Automatically install the necessary tools"** if it appears
5. After installation, **close and reopen PowerShell**
6. Verify:

```powershell
node --version
```

**Expected output**: `v22.x.x` or `v24.x.x`

Also verify npm (Node's package manager):

```powershell
npm --version
```

**Expected output**: `10.x.x` or newer

### On WSL/Ubuntu (alternative)

If you prefer using WSL (Windows Subsystem for Linux):

```bash
# Install Node.js v24 using NodeSource
curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -
sudo apt install -y nodejs

# Verify
node --version
npm --version
```

---

## 1.2 — Get an LLM API Key

OpenClaw needs an AI model to "think." It doesn't include one — you bring your own API key. Think of it like this: OpenClaw is the assistant's body, and the API key gives it a brain.

### Which provider to choose?

| Provider | Model | Best for | Approximate cost |
|----------|-------|----------|-----------------|
| **Anthropic** (recommended) | Claude | Best overall quality | ~$3 per million tokens |
| OpenAI | GPT-4o | Good alternative | ~$2.50 per million tokens |
| Google | Gemini | Budget option | ~$1.25 per million tokens |

> **What are tokens?** Tokens are chunks of text. A typical day of casual use might cost $0.50-$5 depending on how much you chat.

### Get an Anthropic API key (recommended)

1. Go to https://console.anthropic.com
2. Click **Sign Up** (or **Log In** if you have an account)
3. After logging in, click **API Keys** in the left sidebar
4. Click **Create Key**
5. Give it a name like `openclaw`
6. **Copy the key** — it starts with `sk-ant-...`
7. **Save it somewhere safe** (you'll need it during setup)

> **Important**: Set a spending limit! Go to **Plans & Billing** > **Spend Limits** and set a monthly limit (e.g., $20) to avoid surprise charges.

### Get an OpenAI API key (alternative)

1. Go to https://platform.openai.com
2. Sign up or log in
3. Go to **API Keys** (left sidebar)
4. Click **Create new secret key**
5. Copy and save the key (starts with `sk-...`)

---

## 1.3 — Get a Phone Number (for WhatsApp)

If you want OpenClaw to be available on **WhatsApp**, you need a **dedicated phone number** — not your personal one.

### Why a separate number?

- OpenClaw takes over the WhatsApp account on that number
- You can't use WhatsApp normally on a number that's connected to OpenClaw
- Using your personal number means losing access to your regular WhatsApp chats

### Options for getting a number

| Option | Cost | Difficulty |
|--------|------|------------|
| Cheap prepaid SIM card | $5-15 one-time | Easy — buy at any phone store |
| eSIM (digital SIM) | $3-10 one-time | Easy — buy online, no physical card |
| Google Voice (US only) | Free | Medium — requires Google account |

You only need the number to **receive one SMS** during setup (for WhatsApp verification). After that, it doesn't need to stay in a phone.

> **Skip this** if you only plan to use Telegram or Discord (no phone number needed for those).

---

## 1.4 — (Optional) Set Up WSL2

If you're on Windows and prefer to run OpenClaw in Linux (some advanced features work better), you can set up WSL2:

```powershell
# Run in PowerShell as Administrator
wsl --install -d Ubuntu
```

After installation, restart your computer, then open **Ubuntu** from the Start menu and create a username/password.

> **Note**: This is optional. OpenClaw runs fine directly on Windows via PowerShell.

---

## Checklist

Before moving to the next step, make sure you have:

- [ ] **Node.js v22.14+** installed (`node --version` shows correct version)
- [ ] **npm** installed (`npm --version` works)
- [ ] **An API key** from Anthropic, OpenAI, or Google (saved somewhere safe)
- [ ] **(Optional)** A dedicated phone number for WhatsApp

---

**Next step**: [02-install-openclaw.md](02-install-openclaw.md) — Install OpenClaw on your machine
