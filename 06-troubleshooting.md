# Step 6: Troubleshooting

## Common errors and how to fix them

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
