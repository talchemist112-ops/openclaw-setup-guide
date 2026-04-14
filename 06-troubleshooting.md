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

---

### OpenClaw in Ubuntu cannot access GitHub repos

**What it means**: OpenClaw is running inside WSL Ubuntu, but git in that Ubuntu user does not have working GitHub credentials.

**What we saw on this machine**:
- Repo remote used HTTPS: `https://github.com/talchemist112-ops/openclaw-setup-guide.git`
- Ubuntu had no git identity configured
- `gh` was not installed in Ubuntu
- SSH to GitHub failed from WSL1: `ssh: connect to host github.com port 22: Connection refused`

**Best working fix on this machine**: use native GitHub CLI login in Ubuntu with the browser/device-code flow.

**Why this is the preferred path**:
- It gives OpenClaw a real Ubuntu-side GitHub session
- It avoids Windows credential popups during non-interactive git operations
- It matches the login flow the user already used on Windows: browser link + one-time code

**Setup that worked**:
```bash
sudo apt-get update
sudo apt-get install -y gh
gh auth login --hostname github.com --web
gh auth setup-git
```

During login on this machine, the browser opener failed because no WSL browser opener was installed:
```text
Failed opening a web browser at https://github.com/login/device
exec: "xdg-open,x-www-browser,www-browser,wslview": executable file not found in $PATH
```

That was still fine:
- `gh` printed a one-time code
- the GitHub device URL was opened manually in the Windows browser
- login completed successfully

**How to verify**:
```bash
gh auth status
git config --global --list | grep '^credential'
git -C /mnt/c/Users/salman/GitHub/salman/openclaw-setup-guide ls-remote origin HEAD
GIT_TERMINAL_PROMPT=0 git -c credential.interactive=never -c core.askPass=echo -C /mnt/c/Users/salman/GitHub/salman/openclaw-setup-guide push --dry-run origin main
```

**Expected working git helper**:
```text
credential.https://github.com.helper=
credential.https://github.com.helper=!/usr/bin/gh auth git-credential
```

**Successful result we got**:
- `gh auth status` showed login as `talchemist112-ops`
- `git push --dry-run origin main` returned `Everything up-to-date`

**First workaround we tried**: make Ubuntu git reuse the existing Windows Git Credential Manager instead of creating a second separate GitHub login just for WSL/OpenClaw.

**Why this works**:
- OpenClaw inherits the environment of the Ubuntu user it runs as
- If git works in WSL for user `salman`, OpenClaw can use the same repo auth
- This machine already has Windows Git Credential Manager installed at:
  `/mnt/c/Users/salman/AppData/Local/Programs/Git/mingw64/bin/git-credential-manager.exe`

**Setup**:
```bash
git config --global user.name 'salman'
git config --global user.email 'talchemist112@gmail.com'
git config --global credential.helper '/mnt/c/Users/salman/AppData/Local/Programs/Git/mingw64/bin/git-credential-manager.exe'
git config --global credential.https://github.com.useHttpPath true
```

**Read test that worked**:
```bash
git -C /mnt/c/Users/salman/GitHub/salman/openclaw-setup-guide ls-remote origin HEAD
```

Expected result:
- A commit hash followed by `HEAD`
- On this machine it returned `2124761c77d37543ff1cc5f6991df0e7059cf7e5	HEAD`

**Why this was not enough**:
- A non-interactive `git push --dry-run origin main` still failed with:
  `remote: Invalid username or token. Password authentication is not supported for Git operations.`
- In other words, the Windows credential bridge was good enough for read access but not reliable enough for Ubuntu/OpenClaw write access

**Fallback if `gh` login breaks later**: use a dedicated SSH key in WSL and route GitHub SSH over port 443.

**Why we needed port 443**:
- Direct SSH from this WSL1 environment failed with:
  `ssh: connect to host github.com port 22: Connection refused`

**SSH-over-443 setup**:
```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
ssh-keygen -t ed25519 -N '' -f ~/.ssh/id_ed25519_openclaw_github -C 'openclaw-wsl-salman-github'
cat >> ~/.ssh/config <<'EOF'
Host github.com
  HostName ssh.github.com
  Port 443
  User git
  IdentityFile ~/.ssh/id_ed25519_openclaw_github
  IdentitiesOnly yes
EOF
chmod 600 ~/.ssh/config ~/.ssh/id_ed25519_openclaw_github ~/.ssh/id_ed25519_openclaw_github.pub
```

**Public key to add in GitHub**:
```bash
cat ~/.ssh/id_ed25519_openclaw_github.pub
```

Then add that key in GitHub:
- GitHub -> Settings -> SSH and GPG keys -> New SSH key

**After adding the key, switch the repo remote to SSH**:
```bash
git -C /mnt/c/Users/salman/GitHub/salman/openclaw-setup-guide remote set-url origin git@github.com:talchemist112-ops/openclaw-setup-guide.git
```

**Then test**:
```bash
ssh -T git@github.com
git -C /mnt/c/Users/salman/GitHub/salman/openclaw-setup-guide ls-remote origin HEAD
git -C /mnt/c/Users/salman/GitHub/salman/openclaw-setup-guide push --dry-run origin main
```

**Notes**:
- There does not appear to be a dedicated OpenClaw UI setting for GitHub repo auth on this install
- The reliable place to configure repo access is the Ubuntu user account that OpenClaw runs under
- The preferred path on this machine is native `gh` auth in Ubuntu
- If Windows GitHub sign-in changes later, Ubuntu/OpenClaw will keep working because it now has its own `gh` login
- SSH-over-443 remains a useful fallback on this machine because direct SSH to port 22 is blocked
- Do NOT copy GitHub tokens into this repo or into tracked docs

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
