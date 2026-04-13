# Step 4: Connect WhatsApp and/or Telegram

## What you'll do

Connect a messaging app so you can chat with your OpenClaw assistant from your phone or any device — just like texting a friend.

---

## Prerequisites

- Completed [Step 3: Onboarding](03-onboarding-config.md)
- Gateway is running (`openclaw gateway status` shows it's active)
- For WhatsApp: a **dedicated phone number** (see [Step 1, section 1.3](01-prerequisites.md))

---

## Option A: Connect WhatsApp

### A.1 — Install the WhatsApp plugin

```powershell
openclaw plugins install @openclaw/whatsapp
```

Or add the channel directly:

```powershell
openclaw channels add --channel whatsapp
```

### A.2 — Set up WhatsApp on the dedicated phone

If you haven't already:
1. Install **WhatsApp** on a phone (can be a cheap/old phone)
2. Register it with your **dedicated phone number** (not your personal number)
3. Complete the verification SMS

### A.3 — Link OpenClaw to WhatsApp

```powershell
openclaw channels login --channel whatsapp
```

**What happens**: A QR code appears in your terminal.

1. Open **WhatsApp** on the phone with the dedicated number
2. Go to **Settings** > **Linked Devices** > **Link a Device**
3. **Scan the QR code** shown in your terminal

**Expected output**: After scanning, you'll see a success message like:
```
WhatsApp connected successfully
```

### A.4 — Configure who can message your assistant

This is critical for security — you don't want random people controlling your AI assistant.

Edit the config file:

**Windows (PowerShell)**:
```powershell
notepad $env:USERPROFILE\.openclaw\openclaw.json
```

**Or use the CLI**:
```powershell
openclaw config edit
```

Add/update the WhatsApp section:

```json5
{
  channels: {
    whatsapp: {
      // Only allow messages from YOUR personal phone number
      dmPolicy: "allowlist",
      allowFrom: ["+1XXXXXXXXXX"],  // Replace with YOUR personal phone number in E.164 format

      // Group chat settings
      groupPolicy: "disabled"  // Disable group chats for now
    }
  }
}
```

> **E.164 format** means: `+` followed by country code and number with no spaces.
> - US example: `+15551234567`
> - UK example: `+447911123456`
> - India example: `+919876543210`

### A.5 — Test WhatsApp

From your **personal phone** (the number you put in `allowFrom`):

1. Open WhatsApp
2. Start a new chat with the **dedicated number** (the one connected to OpenClaw)
3. Send: `Hello, are you there?`

You should get a response from your AI assistant within a few seconds!

### A.6 — Check connection status

```powershell
openclaw channels status
```

**Expected output**: Shows WhatsApp as `connected`.

---

## Option B: Connect Telegram

Telegram is simpler — no second phone number needed.

### B.1 — Create a Telegram Bot

1. Open Telegram on your phone or desktop
2. Search for **@BotFather** and start a chat
3. Send: `/newbot`
4. BotFather will ask for a **name** — type something like `My OpenClaw Assistant`
5. BotFather will ask for a **username** — type something like `my_openclaw_bot` (must end in `bot`)
6. **Copy the API token** BotFather gives you — it looks like `123456789:ABCdefGhIJKlmNoPQRsTUVwxyz`

### B.2 — Install the Telegram plugin

```powershell
openclaw plugins install @openclaw/telegram
```

### B.3 — Configure Telegram

Edit the config file:

```powershell
openclaw config edit
```

Add the Telegram section:

```json5
{
  channels: {
    telegram: {
      // Paste the token from BotFather
      token: "123456789:ABCdefGhIJKlmNoPQRsTUVwxyz",

      // Only allow messages from your Telegram user ID
      dmPolicy: "allowlist",
      allowFrom: ["YOUR_TELEGRAM_USER_ID"]
    }
  }
}
```

> **How to find your Telegram user ID**: Search for `@userinfobot` on Telegram, start a chat, and it will tell you your numeric ID.

### B.4 — Restart the gateway

```powershell
openclaw gateway restart
```

### B.5 — Test Telegram

1. Open Telegram
2. Search for your bot's username (e.g., `@my_openclaw_bot`)
3. Send: `/start` then `Hello!`

You should get a response from your AI assistant!

---

## Option C: Connect Discord

### C.1 — Create a Discord Bot

1. Go to https://discord.com/developers/applications
2. Click **New Application**, give it a name
3. Go to **Bot** tab > **Reset Token** > copy the token
4. Go to **OAuth2** > **URL Generator** > check `bot` and `messages` > copy the invite URL
5. Open the invite URL to add the bot to your Discord server

### C.2 — Configure Discord

```powershell
openclaw plugins install @openclaw/discord
openclaw config edit
```

Add:
```json5
{
  channels: {
    discord: {
      token: "YOUR_DISCORD_BOT_TOKEN",
      dmPolicy: "allowlist",
      allowFrom: ["YOUR_DISCORD_USER_ID"]
    }
  }
}
```

### C.3 — Restart and test

```powershell
openclaw gateway restart
```

Send a DM to your bot on Discord.

---

## Verify success

```powershell
# Check all connected channels
openclaw channels status
```

**Expected output**: Your connected channels show as `connected` or `online`.

```powershell
# Check overall status
openclaw status
```

---

## Security checklist

- [ ] `allowFrom` is set to **only your phone number / user ID** (never leave it open)
- [ ] `dmPolicy` is set to `allowlist` (not `open`)
- [ ] `groupPolicy` is `disabled` unless you specifically need group chats
- [ ] You've tested that the assistant responds only to YOUR messages

---

**Next step**: [05-daily-usage.md](05-daily-usage.md) — How to use OpenClaw day-to-day
