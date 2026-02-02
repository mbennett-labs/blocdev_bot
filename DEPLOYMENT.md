# blocdev_bot — Deployment & Maintenance Guide

## AWS EC2 Setup

### Instance Details

| Setting | Value |
|---------|-------|
| **Instance ID** | i-0a3669c59fb773431 |
| **Instance Name** | clawdbot |
| **Region** | us-east-2 (Ohio) |
| **Public IP** | 52.15.112.255 |
| **Private IP** | 172.31.1.13 |
| **OS** | Ubuntu 24 |
| **Access** | AWS Console → EC2 Instance Connect |

### Security Group Recommendations

- **SSH (22):** Restrict to your IP only
- **OpenClaw Gateway (18789):** Localhost only (127.0.0.1) unless exposing via Tailscale/tunnel
- **Outbound:** Allow HTTPS (443) for API calls
- Block all other inbound traffic

## Installation Steps

### 1. System Update
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install Node.js 22
```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs
node --version  # Should be 22.x
```

### 3. Install OpenClaw
```bash
sudo npm install -g openclaw@latest
```

### 4. Onboard
```bash
openclaw onboard --install-daemon
```
- Select **Anthropic** as model/auth provider
- Paste your API key when prompted
- Skip skill dependencies (install later)

### 5. Verify
```bash
openclaw status --all
```

Expected: Gateway running on port 18789, auth configured, no channel issues.

## Common Operations

### Start/Stop Gateway
```bash
openclaw gateway --port 18789 --verbose   # Start
openclaw gateway stop                      # Stop
```

### Health Check
```bash
openclaw status --all
```

### View Logs
```bash
# Check npm debug log location from error messages
cat /home/ubuntu/.npm/_logs/<latest-log-file>

# Gateway logs (if running in foreground with --verbose)
# Or check systemd journal if daemon-installed:
journalctl --user -u openclaw-gateway -f
```

### Full Reset (Nuclear Option)
```bash
pkill -f openclaw
rm -rf /home/ubuntu/.openclaw
openclaw onboard --install-daemon
```

### Update OpenClaw
```bash
sudo npm install -g openclaw@latest
openclaw gateway stop
openclaw gateway --port 18789 --verbose
```

### Install a Skill
```bash
openclaw skill install <skill-name>
```

## Config File Location

```
/home/ubuntu/.openclaw/openclaw.json
```

**CAUTION:** Do not manually edit this file unless you know the JSON structure. Use `openclaw configure --section <section>` instead. If the file gets corrupted, delete it and re-onboard.

## Moltbook Registration (✅ Complete)

Registered as **CrawDaddyDev** on Moltbook.

### How it was done:
1. Visited [moltbook.com](https://moltbook.com) → clicked **manual** tab
2. Sent this to bot via Telegram: `Read https://moltbook.com/skill.md and follow the instructions to join Moltbook`
3. Agent self-registered as CrawDaddyDev (original name was taken)
4. Clicked claim link provided by agent
5. Completed human verification
6. Posted verification tweet from @QuarkMichael with code `coral-LGKU`

**Note:** The `npx molthub@latest install moltbook` method did NOT work. Use the manual/skill.md method instead.

## Monitoring & Cost Management

### Anthropic API
- Dashboard: [console.anthropic.com](https://console.anthropic.com)
- Set monthly spending limit (recommend: $10-20 to start)
- Monitor token usage weekly

### AWS
- Free tier covers t2.micro/t3.micro for 12 months
- Monitor instance hours in AWS Billing dashboard
- Set billing alerts at $5 and $10

## Troubleshooting

| Issue | Fix |
|-------|-----|
| `EACCES` permission error | Use `sudo` for global npm installs |
| `install.cmd not found` | You're running Windows commands on Linux — use npm |
| JSON config corrupted | Delete `~/.openclaw/openclaw.json` and re-onboard |
| Token mismatch error | Run `openclaw configure --section gateway` |
| Gateway port in use | `openclaw gateway stop` then restart |
| Tailscale: off | Ignore — not required for basic setup |
| Telegram "access not configured" | Approve pairing: `openclaw pairing approve telegram <CODE>` |
| Moltbook npx install fails | Use manual method: send skill.md URL to bot via Telegram |
| CLI unresponsive | `Ctrl+C`, then `pkill -f openclaw` |

## References

- [OpenClaw Docs](https://docs.openclaw.ai/start/getting-started)
- [OpenClaw Troubleshooting](https://docs.openclaw.ai/troubleshooting)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [Moltbook](https://moltbook.com)
