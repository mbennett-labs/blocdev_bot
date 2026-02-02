# 🤖 blocdev_bot

**An autonomous AI agent powered by [OpenClaw](https://openclaw.ai) — cybersecurity & blockchain focused, live on [Moltbook](https://moltbook.com).**

Built and operated by [Quantum Shield Labs LLC](https://www.linkedin.com/company/110665091)

---

## Overview

blocdev_bot is an autonomous AI agent deployed on AWS EC2, designed to participate in the Moltbook agent social network and serve as a thought leadership presence for cybersecurity, post-quantum cryptography, and blockchain/DeFi topics.

## Tech Stack

| Component | Detail |
|-----------|--------|
| **Runtime** | OpenClaw (formerly Clawdbot/Moltbot) |
| **Model** | anthropic/claude-opus-4-5 |
| **Infrastructure** | AWS EC2 (Ubuntu 24, t-series free tier) |
| **Region** | us-east-2 (Ohio) |
| **Node.js** | v22+ |
| **Platform** | Moltbook — the front page of the agent internet |

## Agent Identity

| Field | Value |
|-------|-------|
| **Bot Name** | blocdev_bot |
| **Operator** | Quantum Shield Labs LLC |
| **Focus Areas** | Post-quantum cryptography, healthcare cybersecurity, blockchain/DeFi, Base ecosystem |
| **Verification** | X/Twitter: [@QuarkMichael](https://x.com/QuarkMichael) |
| **Telegram** | [@blocdev_bot](https://t.me/blocdev_bot) |
| **Moltbook Name** | CrawDaddyDev 🦞 |
| **Bot Persona** | CrawDaddy |

## Deployment

### Prerequisites

- AWS EC2 instance (Ubuntu 24, 8GB RAM minimum)
- Node.js 22+
- Anthropic API key ([console.anthropic.com](https://console.anthropic.com))
- X/Twitter account for Moltbook verification

### Quick Start

```bash
# Install Node.js 22
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs

# Install OpenClaw
sudo npm install -g openclaw@latest

# Run onboarding wizard
openclaw onboard --install-daemon

# Check status
openclaw status --all

# Start gateway (if not auto-started)
openclaw gateway --port 18789 --verbose
```

### Moltbook Registration (✅ Complete)

1. ~~Visit [moltbook.com](https://moltbook.com) and copy the skill.md link~~
2. ~~Send to agent via Telegram: `Read https://moltbook.com/skill.md and follow the instructions to join Moltbook`~~
3. ~~Agent self-registers as CrawDaddyDev~~
4. ~~Complete human verification + X/Twitter verification post~~
5. Bot is live — auto-posting, engaging, earning karma

## Content Strategy

blocdev_bot focuses on:

- **Post-quantum cryptography** — NIST standards, migration timelines, healthcare implications
- **Healthcare cybersecurity** — HIPAA, threat landscape, DMV-area healthcare org security
- **Blockchain & DeFi** — Base ecosystem, x402 micropayments, smart contract security
- **Emerging tech commentary** — AI agents, MCP protocol, Web3 security patterns

## Security Considerations

- Agent runs on **isolated EC2 instance** — not on local machine
- No sensitive credentials stored on the instance beyond API keys
- Security group locked down to essential ports only
- Regular `openclaw status --all` health checks
- Monitor Anthropic API usage via console dashboard

## File Structure

```
blocdev_bot/
├── README.md                 # This file
├── AGENT_SPEC.md             # Detailed agent specification & persona
├── DEPLOYMENT.md             # Full deployment & maintenance guide
├── config/
│   └── openclaw.example.json # Example config (no real keys)
└── skills/
    └── custom/               # Custom skills (future)
```

## Links

- [OpenClaw Docs](https://docs.openclaw.ai)
- [Moltbook](https://moltbook.com)
- [Quantum Shield Labs — LinkedIn](https://www.linkedin.com/company/110665091)
- [Operator — X/Twitter](https://x.com/QuarkMichael)
- [Operator — Farcaster](https://warpcast.com/freakid)

## License

MIT
