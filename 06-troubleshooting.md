# Step 6: Troubleshooting

## Common errors and how to fix them

---

### WSL1-specific issues (Windows Server 2022)

These issues were encountered during actual deployment on Windows Server 2022 build 20348:

**`wsl --install -d Ubuntu` fails with error 0x80072ee7**
- This is a DNS/network issue blocking Microsoft Store downloads
- **Fix**: Manually download Ubuntu rootfs and import it:
```powershell
# Download rootfs (from Windows)
curl -L -o C:\Users\salman\Downloads\ubuntu-rootfs.tar.gz "https://cloud-images.ubuntu.com/wsl/jammy/current/ubuntu-jammy-wsl-amd64-ubuntu22.04lts.rootfs.tar.gz"

# Import into WSL (run from PowerShell, NOT bash)
powershell.exe -Command "& { wsl.exe --import Ubuntu C:\Users\salman\WSL\Ubuntu C:\Users\salman\Downloads\ubuntu-rootfs.tar.gz }"
```

**`wsl --set-default-version 2` and `wsl --set-version Ubuntu 2` print help text instead of working**
- Known bug in the WSL build shipped with Windows Server 2022
- WSL1 works fine for OpenClaw — just no systemd (start gateway manually)

**Node.js v24: "cannot execute binary file: Exec format error"**
- Node.js v24 binaries are incompatible with WSL1
- **Fix**: Use Node.js v22 instead:
```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo bash -
sudo apt install -y nodejs
```

**Imported distro runs as root by default**
- Create a user and set it as default:
```bash
useradd -m -s /bin/bash salman
echo 'salman ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
echo -e '[user]\ndefault=salman' > /etc/wsl.conf
```
- Then restart WSL: `wsl --shutdown` (from PowerShell)

---

### "openclaw" command not found

**What it means**: OpenClaw isn't installed or isn't in your system PATH.

**Fix (Windows)**:
```powershell
# Reinstall
npm install -g openclaw@latest

# If still not found, check where npm puts global packages
npm config get prefix

# Add that path to your system PATH if needed
```

**Fix (Linux/macOS)**:
```bash
# Check if it's installed somewhere
which openclaw || npx openclaw --version

# Reinstall
npm install -g openclaw@latest
```

---

### Gateway not starting / port 18789 in use

**What it means**: Something else is using port 18789, or the gateway crashed.

**Fix**:
```powershell
# Check what's using the port (Windows)
netstat -ano | findstr 18789

# Kill the process using that PID
taskkill /PID <PID_NUMBER> /F

# Restart the gateway
openclaw gateway --port 18789
```

```bash
# Check what's using the port (Linux)
lsof -i :18789

# Kill it
kill -9 <PID>

# Restart
openclaw gateway --port 18789
```

---

### WhatsApp QR code not appearing

**What it means**: The WhatsApp plugin isn't installed or the gateway isn't running.

**Fix**:
```powershell
# Make sure gateway is running
openclaw gateway status

# Reinstall WhatsApp plugin
openclaw plugins install @openclaw/whatsapp

# Try login again
openclaw channels login --channel whatsapp
```

---

### WhatsApp disconnects frequently

**What it means**: The session keeps dropping.

**Fix**:
```powershell
# Check logs for errors
openclaw logs --follow

# Logout and re-link
openclaw channels logout --channel whatsapp
openclaw channels login --channel whatsapp
```

Also make sure:
- Your internet connection is stable
- The gateway process hasn't crashed (check `openclaw gateway status`)
- WhatsApp on the phone hasn't been logged out

---

### Assistant not responding to messages

**Check these in order:**

1. **Is the gateway running?**
```powershell
openclaw gateway status
```

2. **Is the channel connected?**
```powershell
openclaw channels status
```

3. **Are you in the allowFrom list?**
Check `~/.openclaw/openclaw.json` — your phone number must be in the `allowFrom` array.

4. **Is the API key valid?**
```powershell
openclaw doctor
```

5. **Check the logs for errors:**
```powershell
openclaw logs --follow
```

---

### "API key invalid" or authentication errors

**What it means**: Your AI provider API key is wrong or expired.

**Fix**:
```powershell
# Re-run onboarding to reset the API key
openclaw onboard
```

Or manually edit the config:
```powershell
openclaw config edit
```

And update the API key in the provider section.

---

### Anthropic API key has low balance / OpenAI quota exceeded

**What it means**: Your auth file is valid, but the provider is rejecting requests because billing is exhausted.

**What we saw**:
- Anthropic: `Your credit balance is too low to access the Anthropic API`
- OpenAI: `You exceeded your current quota`

This means:
- The API keys are present and readable
- OpenClaw is configured correctly
- The failure is now billing/quota, not local setup

**Fix / workaround**: Use **Claude Code OAuth** instead of the Anthropic API key.

**Important note for this machine**:
- The Claude Code OAuth source file is `C:\Users\salman\.claude\.credentials.json`
- On this machine that file appears to come from the VS Code Claude Code extension (`anthropic.claude-code-*`)
- OpenClaw is not authenticating through VS Code directly; it is reusing the credential stored in that file

**How we achieved it**:
```powershell
# Make sure Claude Code is already logged in on Windows.
# Expected source file:
C:\Users\salman\.claude\.credentials.json
```

```bash
# Add the Claude Code OAuth credential to OpenClaw's auth store
# as a new Anthropic profile named anthropic:claude-cli.
node -e "
const fs=require('fs'), os=require('os');
const storePath=os.homedir()+'/.openclaw/agents/main/agent/auth-profiles.json';
const claudePath='/mnt/c/Users/salman/.claude/.credentials.json';
const backup=storePath+'.bak-before-claude-code-profile';
const store=JSON.parse(fs.readFileSync(storePath,'utf8'));
const creds=JSON.parse(fs.readFileSync(claudePath,'utf8')).claudeAiOauth;
fs.copyFileSync(storePath, backup);
store.version=store.version || 1;
store.profiles=store.profiles || {};
store.profiles['anthropic:claude-cli'] = {
  type: 'oauth',
  provider: 'anthropic',
  access: creds.accessToken,
  refresh: creds.refreshToken,
  expires: creds.expiresAt,
  managedBy: 'claude-code'
};
fs.writeFileSync(storePath, JSON.stringify(store, null, 2) + '\n', { mode: 0o600 });
fs.chmodSync(storePath, 0o600);
"

# Tell OpenClaw to prefer Claude Code for Anthropic requests.
openclaw models auth order set --provider anthropic anthropic:claude-cli anthropic:default

# Switch back to the Claude model.
openclaw config set agents.defaults.model anthropic/claude-sonnet-4-20250514

# Restart the gateway.
tmux kill-session -t openclaw 2>/dev/null || true
tmux new-session -d -s openclaw 'source ~/.bashrc >/dev/null 2>&1; openclaw gateway --port 18789 2>&1 | tee /tmp/openclaw-gw-live.log'
```

**Verify**:
```bash
# Check the current model
openclaw config get agents.defaults.model

# Check Anthropic auth order
openclaw models auth order get --provider anthropic

# Send a tiny test prompt
openclaw agent --agent main --message 'Reply with exactly: OK' --timeout 180
```

If it works, the final command should return:

```text
OK
```

**Don't try**:
- Don't keep debugging `auth-profiles.json` if the log clearly says `credit balance is too low` or `quota exceeded`
- Don't assume old dashboard log entries are current after a gateway restart
- Don't commit `C:\Users\salman\.claude\.credentials.json` or copied OAuth tokens to git

If OpenClaw later says `claude-code credential is unavailable; re-authenticate in the external CLI and retry`, re-authenticate Claude Code on Windows first so `C:\Users\salman\.claude\.credentials.json` gets refreshed, then re-sync OpenClaw from that file.

If Windows Claude Code is already working but OpenClaw still shows the same refresh error, compare the expiry in `C:\Users\salman\.claude\.credentials.json` with the copied `anthropic:claude-cli` entry in `/home/salman/.openclaw/agents/main/agent/auth-profiles.json`. If the Windows file is newer, re-sync OpenClaw from the Windows file and clear the cached failure state in `/home/salman/.openclaw/agents/main/agent/auth-state.json`.

---

### High API costs / runaway spending

**What happened**: OpenClaw made too many API calls (especially if heartbeat/proactive mode is on).

**Immediate fix**:
```powershell
# Stop the gateway immediately
openclaw gateway stop

# Or kill all openclaw processes
taskkill /IM openclaw* /F   # Windows
pkill openclaw              # Linux
```

**Prevent it**:
1. Set spending limits on your API provider's dashboard
2. Disable heartbeat until you're comfortable:
```json5
{
  heartbeat: {
    every: "0m"
  }
}
```
3. Start with a cheaper model like `claude-haiku-4-5-20251001`

---

### "Permission denied" errors on Linux/WSL

**Fix**:
```bash
# Fix ownership of OpenClaw config
sudo chown -R $USER:$USER ~/.openclaw

# Fix npm global permissions
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

---

### Node.js version too old

**What it means**: You need Node.js v22.14 or newer.

**Fix**:
```powershell
# Check current version
node --version

# If below v22.14, download the latest from https://nodejs.org
# Or use nvm (Node Version Manager):
```

**Windows**:
Download the latest LTS from https://nodejs.org and run the installer.

**Linux**:
```bash
curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -
sudo apt install -y nodejs
```

---

### Dashboard won't open in browser

**Fix**:
```powershell
# Check the gateway is running
openclaw gateway status

# Manually open the URL
# Default: http://localhost:18789
```

If using WSL, the dashboard might be at `http://localhost:18789` on your Windows browser (WSL forwards ports automatically).

---

### Messages arrive but responses are slow

**Possible causes**:

1. **Model is too heavy** — Switch to a faster model:
```json5
{
  agent: {
    model: "anthropic/claude-haiku-4-5-20251001"  // Fastest
  }
}
```

2. **Network latency** — Check your internet speed

3. **Gateway overloaded** — Restart:
```powershell
openclaw gateway restart
```

---

## Full diagnostic

When all else fails, run the complete diagnostic:

```powershell
openclaw doctor
openclaw status --all
openclaw logs --follow
```

Share the output from these commands when asking for help.

---

## Getting help

- **Official docs**: https://docs.openclaw.ai
- **GitHub Issues**: https://github.com/openclaw/openclaw/issues
- **Community Discord**: https://discord.gg/clawd
- **Skill Hub**: https://clawhub.ai

---

## Nuclear option: Start completely fresh

If you want to wipe everything and start over:

```powershell
# Uninstall OpenClaw
npm uninstall -g openclaw

# Delete all config and data
# WARNING: This deletes all your settings, workspace, and connected accounts!
```

**Windows**:
```powershell
Remove-Item -Recurse -Force $env:USERPROFILE\.openclaw
```

**Linux/macOS**:
```bash
rm -rf ~/.openclaw
```

Then go back to [Step 2](02-install-openclaw.md) and start again.
