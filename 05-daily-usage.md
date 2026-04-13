# Step 5: Using OpenClaw Day-to-Day

## What you'll do

Learn how to actually use your OpenClaw assistant — what to ask it, how to connect services like Gmail and Calendar, and how to get the most out of it.

---

## Prerequisites

- Completed [Step 4: Channel Setup](04-channel-setup.md)
- You can send/receive messages with your assistant via WhatsApp, Telegram, or the dashboard

---

## Basics — Talking to Your Assistant

Just message your assistant in natural language. No special commands needed.

### Example conversations

**Email:**
```
You: Check my inbox, anything urgent?
Bot: You have 23 unread emails. 3 look urgent:
     1. Meeting moved to 3pm (from John)
     2. Invoice overdue (from Accounting)
     3. Server alert (from AWS)
     Want me to summarize any of these?

You: Summarize the server alert
Bot: AWS is reporting high CPU usage on your production server...

You: Reply to John saying I'll be there at 3pm
Bot: Done. I've sent: "Hi John, I'll be there at 3pm. Thanks for the heads up."
```

**Calendar:**
```
You: What's on my calendar today?
Bot: You have 3 events today:
     - 10:00 AM — Team standup
     - 1:00 PM — Lunch with Sarah
     - 3:00 PM — Client demo

You: Move the client demo to 4pm
Bot: Done. Moved "Client demo" from 3:00 PM to 4:00 PM.

You: Schedule a meeting with Ahmed tomorrow at 2pm
Bot: Created: "Meeting with Ahmed" tomorrow at 2:00 PM.
```

**Web browsing:**
```
You: Find me the cheapest flight from Dubai to London next Friday
Bot: I found these options on Google Flights:
     1. Emirates — $450 (direct, 7h)
     2. British Airways — $380 (direct, 7.5h)
     3. FlyDubai — $290 (1 stop, 12h)

You: Check me in for my Emirates flight EK203
Bot: I'll need your booking reference and last name...
```

**General tasks:**
```
You: Remind me to call the dentist at 9am tomorrow
You: What's the weather in Dubai this week?
You: Translate "hello, how are you?" to Arabic
You: Summarize this PDF [attach a file]
```

---

## Connecting Services

### Connect Gmail

To let OpenClaw read/send emails, you need to connect your Gmail account.

Message your assistant:
```
You: Connect my Gmail
```

It will guide you through OAuth authentication (you'll click a link and authorize access).

Or configure it manually — see the integrations list at https://docs.openclaw.ai/tools

### Connect Google Calendar

Similar to Gmail:
```
You: Connect my Google Calendar
```

Follow the authentication link it provides.

### Other integrations

OpenClaw supports 50+ integrations. Popular ones:

| Service | What it can do |
|---------|---------------|
| Gmail | Read, send, organize emails |
| Google Calendar | View, create, modify events |
| GitHub | Check PRs, create issues, review code |
| Spotify | Play music, create playlists |
| Notion | Read/write notes and docs |
| Obsidian | Manage your knowledge base |
| Philips Hue | Control smart lights |
| Home Assistant | Smart home automation |

Full list: https://openclaw.ai/integrations

---

## Useful CLI Commands

Run these in PowerShell/Terminal:

| Command | What it does |
|---------|-------------|
| `openclaw status` | Quick health check |
| `openclaw status --all` | Detailed diagnostics |
| `openclaw dashboard` | Open the web UI |
| `openclaw channels status` | Check channel connections |
| `openclaw gateway status` | Check if the gateway is running |
| `openclaw gateway restart` | Restart the gateway |
| `openclaw logs --follow` | Watch live logs (useful for debugging) |
| `openclaw doctor` | Run a full diagnostic |

---

## Customizing Your Assistant

### Change its personality

Edit `~/.openclaw/workspace/SOUL.md`:

```powershell
notepad $env:USERPROFILE\.openclaw\workspace\SOUL.md
```

Example content:
```markdown
You are a professional personal assistant. Be concise and proactive.
Always confirm before sending emails or making calendar changes.
Use a friendly but professional tone.
```

### Tell it about yourself

Edit `~/.openclaw/workspace/USER.md`:

```powershell
notepad $env:USERPROFILE\.openclaw\workspace\USER.md
```

Example content:
```markdown
Name: Salman
Role: Technical presales engineer
Location: Dubai, UAE
Timezone: UTC+4 (Gulf Standard Time)
Email: salman@example.com
Preferences:
- Prefer concise answers
- Always confirm before sending emails
- Meeting reminders 15 minutes early
```

### Change the AI model

Edit `~/.openclaw/openclaw.json` and change the model:

```json5
{
  agent: {
    // For best quality (slower, more expensive)
    model: "anthropic/claude-opus-4-0-20250415"

    // For good balance (recommended)
    // model: "anthropic/claude-sonnet-4-20250514"

    // For speed and lower cost
    // model: "anthropic/claude-haiku-4-5-20251001"
  }
}
```

---

## Proactive Mode (Heartbeat)

OpenClaw can run tasks on its own, without you asking — like checking your inbox every morning.

**Warning**: Start with this disabled until you're comfortable with OpenClaw. It can run up API costs.

To enable:

1. Edit `~/.openclaw/openclaw.json`:
```json5
{
  heartbeat: {
    every: "30m"  // Check every 30 minutes
  }
}
```

2. Edit `~/.openclaw/workspace/HEARTBEAT.md` to define what it should do:
```markdown
Every morning at 8am:
- Check my inbox for urgent emails
- Tell me my calendar for today
- Check the weather

Every Friday at 5pm:
- Summarize the week's emails
```

---

## Tips for best results

1. **Be specific** — "Check my inbox" is better than "do email stuff"
2. **Ask it to confirm** — "Draft a reply to John (show me before sending)"
3. **Teach it over time** — "Remember that I always want meeting notes in bullet points"
4. **Set boundaries** — In SOUL.md, add rules like "Never delete emails without asking"
5. **Start small** — Get comfortable with simple tasks before enabling automation

---

**Having issues?** [06-troubleshooting.md](06-troubleshooting.md) — Common errors and fixes
