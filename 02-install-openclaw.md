# Step 2: Install OpenClaw

## What you'll do

Install the OpenClaw application on your machine. This takes about 2-5 minutes.

---

## Prerequisites

- Completed [Step 1: Prerequisites](01-prerequisites.md)
- Node.js v22.14+ is installed
- You have your API key ready

---

## Choose your installation method

### Method A — One-liner installer (Recommended)

This is the easiest way. It auto-detects your system and installs everything.

**Windows (PowerShell):**

Open PowerShell and run:

```powershell
iwr -useb https://openclaw.ai/install.ps1 | iex
```

**What this does**: Downloads the OpenClaw installer script and runs it. It will install OpenClaw and immediately start the onboarding wizard.

**WSL/Linux/macOS (Terminal):**

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

### Method B — Install via npm

If you prefer more control:

```powershell
# Install OpenClaw globally
npm install -g openclaw@latest
```

**Expected output**: A progress bar followed by installation messages. At the end:
```
added XX packages in XXs
```

Verify it worked:

```powershell
openclaw --version
```

**Expected output**: A version number like `1.x.x`

### Method C — Install from source (for developers)

Only use this if you want to modify OpenClaw's code:

```bash
git clone https://github.com/openclaw/openclaw.git
cd openclaw
pnpm install && pnpm ui:build && pnpm build
pnpm link --global
```

---

## Verify the installation

No matter which method you used, verify OpenClaw is installed:

```powershell
openclaw --version
```

**Expected output**: A version number (e.g., `OpenClaw 2026.4.11 (769908e)`)

```powershell
openclaw doctor
```

**Expected output**: A diagnostic report showing your system info and any issues.

---

## What just happened?

You installed:
- **`openclaw`** — the main CLI (command-line interface) tool
- **OpenClaw Gateway** — the background service that connects everything together
- **Control UI** — a web dashboard you can open in your browser

These all come bundled together. The next step will configure them.

---

## Common issues

### "openclaw is not recognized" (Windows)

Close PowerShell completely and reopen it. If still not working:

```powershell
# Check where npm installs global packages
npm config get prefix
```

Make sure the `bin` folder inside that path is in your system PATH.

### "Permission denied" (Linux/macOS)

```bash
# Fix npm permissions
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

# Then reinstall
npm install -g openclaw@latest
```

---

**Next step**: [03-onboarding-config.md](03-onboarding-config.md) — Run the setup wizard and configure settings
