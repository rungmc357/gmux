# ClawDoc 🦞🩺

A self-hosted Telegram bot that keeps your [OpenClaw](https://openclaw.ai) running. It monitors, diagnoses, and fixes problems automatically.

**[clawdoc.org](https://clawdoc.org)**

---

## What you get

- **Automatic recovery** — checks OpenClaw every 15 minutes. If something breaks, it figures out why, fixes it, and tells you what happened.
- **Remote control** — restart services, check system health, and view logs from anywhere. Everything is a button tap in Telegram.
- **Config rollback** — your config is backed up automatically. If an update breaks something, preview the diff and restore any previous version with one tap.
- **Full diagnostics** — one button runs a complete health check and tells you exactly what's wrong. If it can fix the problem, it gives you a button for that too.
- **Network monitoring** — watches your connection and notifies you when it drops or comes back, along with how long the outage lasted.

---

## When something breaks

ClawDoc works through a fix chain automatically, stopping as soon as the problem is resolved:

1. Check if the config is valid — if it's broken, restore from the most recent backup
2. Restart the gateway and verify it comes back healthy
3. If that didn't work, run OpenClaw's built-in doctor to repair common issues
4. If still down and AI is enabled, diagnose the error logs and try a targeted fix
5. If nothing works, message you with exactly what went wrong

Most issues are resolved at step 1 or 2.

---

## Why it works

```
OS (launchd / systemd)
  └── keeps ClawDoc running
        └── keeps OpenClaw running
```

ClawDoc runs as its own system service, independent of OpenClaw. When OpenClaw goes down, ClawDoc is still there to bring it back.

---

## Get started

**1. Run the installer**

```bash
curl -fsSL https://raw.githubusercontent.com/rungmc357/clawdoc/main/install.sh | bash
```

You'll be prompted for a Telegram bot token. ClawDoc finds your OpenClaw install automatically.

**2. Claim your bot in Telegram**

Send the claim code, choose your preferences, and you're done.

### Let your AI do it

If you use OpenClaw or any AI coding agent:

> *"Install ClawDoc on my machine. Read the AGENTS.md at https://github.com/rungmc357/clawdoc for setup instructions."*

---

## Add AI if you want

Everything works with buttons out of the box. If you have [Ollama](https://ollama.com) running locally, ClawDoc can also understand plain English and do smarter diagnostics.

- *"why is openclaw down?"*
- Error log analysis
- Custom skills — *"whenever I say /deploy, run git pull && pm2 restart all"*

Skip it during setup and add it anytime.

---

## Configuration

Config lives at `~/.config/clawdoc/config.json`. Most settings can be changed via the `/settings` button in Telegram.

```json
{
  "bot_token": "YOUR_BOT_TOKEN",
  "allowed_chat_id": 123456789,
  "ollama_model": "qwen3.5:4b",
  "shell_security": "disabled",
  "shell_session_timeout_min": 10,
  "watchdog_interval_min": 15,
  "network_monitor": true,
  "watched_services": [
    {
      "name": "OpenClaw",
      "url": "http://localhost:18789/health",
      "restart_cmd": "openclaw gateway restart",
      "log_file": "~/.openclaw/logs/gateway.log",
      "config_files": ["~/.openclaw/openclaw.json"]
    }
  ]
}
```

### New commands

- `/lock` — immediately re-lock the shell session (password mode only)
- `/reload` — re-read config from disk without restarting

### Session unlock (password mode)

When `shell_security` is `"password"`, entering your password once unlocks the session for `shell_session_timeout_min` minutes (default 10). During this window, `/run` commands only need tap-to-approve, no password. Use `/lock` to re-lock early. The unlock status and time remaining are shown in `/status` and `/settings`.

### Watchdog loop guard

If a service is auto-restarted 3+ times within 5 minutes, ClawDoc stops restarting it and alerts you instead — preventing restart loops from masking the real issue.

### Rollback diff preview

The `/rollback` command now shows a "Diff" button alongside each restore option. Tap it to see exactly what will change before restoring.

---

## Uninstall

```bash
# Mac
launchctl unload ~/Library/LaunchAgents/io.clawdoc.agent.plist
rm ~/Library/LaunchAgents/io.clawdoc.agent.plist
rm -rf ~/.local/share/clawdoc ~/.config/clawdoc

# Linux
systemctl --user disable --now clawdoc
rm -rf ~/.local/share/clawdoc ~/.config/clawdoc
```

---

## License

MIT — Built by [@thatguygeo](https://x.com/thatguygeo)
