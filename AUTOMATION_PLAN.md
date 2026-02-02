# CrawDaddyDev — Background Automation & Dashboard Plan

**Quantum Shield Labs LLC | February 2, 2026 | Version 1.0**

---

## Executive Summary

This document is the action plan for turning CrawDaddyDev from a bot that sits waiting for commands into an autonomous worker that earns its server time. It covers three phases: automated content and lead generation (this week), a Mocha monitoring dashboard (next week), and advanced integrations (weeks 3–4). Every script, cron job, and configuration is included so you can SSH into the EC2 instance and deploy without guesswork.

---

## Current Infrastructure

| Component | Details |
|-----------|---------|
| Server | AWS EC2 t2.micro (8GB RAM), Ubuntu 24, us-east-1 |
| Bot Runtime | OpenClaw (systemd daemon), Port 18789 |
| LLM Backend | Anthropic API (claude-opus-4-5) |
| Telegram | @blocdev_bot (display: CrawDaddyDev) |
| Moltbook | CrawDaddyDev (claimed, verified via @QuarkMichael) |
| GitHub | mbennett-labs/blocdev_bot |
| Zapier MCP | Account created (not yet connected) |
| Mocha | Account created (not yet connected) |
| Brave Search API | Free AI tier (2,000 queries/month, 1 req/sec) |

---

## Phase 1: Background Automation (This Week)

**Goal:** Get CrawDaddy producing value while you sleep. Two high-impact tasks that directly drive QSL revenue.

### 1A. Automated Content Generation

CrawDaddy generates one LinkedIn post draft per day about post-quantum security, healthcare cybersecurity, or blockchain security. It pushes the draft to you on Telegram for approval. You review on your phone, thumbs up or edit, and it posts. One piece of daily content with zero creative effort from you.

**How It Works:** A shell script runs via cron every morning at 7:00 AM EST. It sends a structured prompt to the OpenClaw agent via its local API, asking it to generate a LinkedIn post based on rotating content pillars. The agent drafts the post and sends it to you on Telegram. You reply with "approve" to post it, "edit" with changes, or "skip" to discard.

**Script:** `/home/ubuntu/scripts/daily-content.sh`

```bash
#!/bin/bash
# CrawDaddyDev Daily Content Generator
# Runs via cron at 7:00 AM EST

PILLARS=(
  "post-quantum cryptography threats to healthcare"
  "HIPAA compliance and quantum computing risk"
  "blockchain security and DeFi vulnerabilities"
  "NIST post-quantum standards update"
  "healthcare data breach trends and prevention"
  "quantum-safe encryption migration strategies"
  "cryptographic agility for healthcare organizations"
)

DAY_INDEX=$(( $(date +%j) % ${#PILLARS[@]} ))
TOPIC="${PILLARS[$DAY_INDEX]}"

curl -s -X POST http://localhost:18789/api/message \
  -H 'Content-Type: application/json' \
  -d "{
    \"message\": \"Generate a LinkedIn post about: $TOPIC. Keep it under 1300 characters. Include a hook in the first line, one key insight, and a call-to-action mentioning Quantum Shield Labs. Make it professional but accessible. Do NOT use hashtags excessively - max 3 relevant ones at the end. Send it to me on Telegram for approval before posting.\"
  }"
```

**Cron Setup:**

```bash
mkdir -p /home/ubuntu/scripts
mkdir -p /home/ubuntu/logs
chmod +x /home/ubuntu/scripts/daily-content.sh
crontab -e
# Add this line (7 AM EST = 12:00 UTC):
0 12 * * * /home/ubuntu/scripts/daily-content.sh >> /home/ubuntu/logs/content.log 2>&1
```

### 1B. Lead & Opportunity Monitoring

CrawDaddy scans for relevant opportunities twice daily: healthcare cybersecurity RFPs, LinkedIn posts mentioning post-quantum security in the DMV area, NIST PQC announcements, and healthcare breach news. When it finds something actionable, it sends you a Telegram summary with links.

**Script:** `/home/ubuntu/scripts/lead-monitor.sh`

```bash
#!/bin/bash
# CrawDaddyDev Lead & Opportunity Monitor
# Runs via cron at 8:00 AM and 2:00 PM EST

curl -s -X POST http://localhost:18789/api/message \
  -H 'Content-Type: application/json' \
  -d '{
    "message": "Run a lead scan. Search for: 1) Recent healthcare cybersecurity RFPs or contracts in DC/MD/VA area 2) LinkedIn posts about post-quantum cryptography or HIPAA compliance 3) New NIST announcements about PQC standards 4) Recent major healthcare data breaches (potential leads) 5) Government contract opportunities related to quantum-safe encryption. Summarize findings and send me the top 3 most actionable items on Telegram with links. If nothing new, just send a brief update."
  }'
```

**Cron Setup:**

```bash
# 8 AM EST = 13:00 UTC, 2 PM EST = 19:00 UTC
0 13 * * * /home/ubuntu/scripts/lead-monitor.sh >> /home/ubuntu/logs/leads.log 2>&1
0 19 * * * /home/ubuntu/scripts/lead-monitor.sh >> /home/ubuntu/logs/leads.log 2>&1
```

### 1C. Gumroad Sales Monitor

Simple daily check on Post-Quantum Security Playbook sales. CrawDaddy checks Gumroad and notifies you of any new purchases so you can follow up with buyers.

**Script:** `/home/ubuntu/scripts/sales-check.sh`

```bash
#!/bin/bash
# CrawDaddyDev Gumroad Sales Monitor
# Runs daily at 9:00 AM EST

curl -s -X POST http://localhost:18789/api/message \
  -H 'Content-Type: application/json' \
  -d '{
    "message": "Check Gumroad for any new sales of the Post-Quantum Security Playbook in the last 24 hours. Report: number of sales, total revenue, and buyer emails (if available) so I can send follow-up outreach. Send the summary to me on Telegram."
  }'
```

**Cron Setup:**

```bash
# 9 AM EST = 14:00 UTC
0 14 * * * /home/ubuntu/scripts/sales-check.sh >> /home/ubuntu/logs/sales.log 2>&1
```

### Phase 1 Deployment Checklist

1. SSH into your EC2 instance
2. Create the scripts directory: `mkdir -p /home/ubuntu/scripts /home/ubuntu/logs`
3. Create each script file (daily-content.sh, lead-monitor.sh, sales-check.sh)
4. Make them executable: `chmod +x /home/ubuntu/scripts/*.sh`
5. Test each script manually first: `bash /home/ubuntu/scripts/daily-content.sh`
6. Verify CrawDaddy sends you a Telegram message for each test
7. Add all three cron jobs: `crontab -e`
8. Confirm cron is running: `crontab -l`
9. Wait for the next scheduled run and verify output in `/home/ubuntu/logs/`

---

## Phase 2: Mocha Dashboard (Next Week)

Once Phase 1 is running and CrawDaddy is producing autonomous output, you will have actual data to display. That is when the Mocha dashboard earns its place as an operational need rather than a nice-to-have.

### Dashboard Layout

A single-page web app published via Mocha with four panels, accessible from your phone or any browser. CrawDaddy pushes updates to the Mocha database via its API integration, and the dashboard reads from the same database.

| Panel | What It Shows | Update Frequency |
|-------|---------------|-----------------|
| Content Queue | Today's draft post, approval status, posted/pending/skipped history | Daily at 7 AM |
| Lead Feed | Latest opportunities found, links, relevance score, action taken | Twice daily |
| Sales Tracker | Playbook sales count, revenue this week/month, buyer follow-up status | Daily at 9 AM |
| Bot Health | Gateway status, uptime, last heartbeat, API credit usage, error count | Every 5 minutes |

### How to Connect Mocha

Mocha provides a built-in database and publish flow. The approach is to have CrawDaddy write status updates to a JSON file on EC2, then use a lightweight API endpoint (or Zapier webhook) to push that data into Mocha's database. The dashboard reads from the database and renders the panels.

1. Create a Mocha project called "CrawDaddyDev Dashboard"
2. Set up four database tables: content_queue, leads, sales, bot_health
3. Add a Zapier webhook trigger that CrawDaddy posts to after each task completes
4. Zapier routes the data to the correct Mocha database table
5. Build the dashboard UI in Mocha using its component library
6. Publish and bookmark the URL on your phone

### Cost Estimate

| Item | Cost |
|------|------|
| Mocha Bronze Plan | $20/month (includes database, auth, publishing) |
| Zapier Free Tier | $0 (100 tasks/month, more than enough for Phase 1–2) |
| Anthropic API (CrawDaddy) | ~$5–15/month for scheduled tasks (depends on usage) |
| AWS EC2 Free Tier | $0 (first 12 months) |
| Brave Search API Free Tier | $0 (2,000 queries/month) |
| **Total Monthly** | **$25–35/month** |

---

## Phase 3: Advanced Integrations (Weeks 3–4)

These are the stretch goals once Phases 1 and 2 are stable and producing results. Each one adds capability but also adds complexity, so only layer these on when the foundation is solid.

### 3A. Automated LinkedIn Posting

Currently Phase 1 generates drafts and you manually post them. Phase 3 closes the loop: CrawDaddy posts directly to LinkedIn via the LinkedIn API or a Zapier integration. You still get the Telegram notification but instead of copy-pasting, you just approve and it posts automatically.

### 3B. Weekly Research Digest

CrawDaddy compiles a weekly briefing every Sunday evening covering the latest quantum computing threats, NIST PQC updates, healthcare breaches, and relevant industry moves. This becomes both your personal intelligence report and content fuel for the following week. It can also be repurposed as a newsletter for QSL clients.

### 3C. Client Pipeline Tracker

Add a fifth panel to the Mocha dashboard that tracks prospective clients through your sales pipeline: initial contact, consultation scheduled, proposal sent, contract signed. CrawDaddy helps by auto-populating leads from the monitoring system and you update status manually. This replaces the need for a separate CRM at your current scale.

### 3D. Moltbook Auto-Engagement

CrawDaddy autonomously posts to Moltbook on schedule, responds to other agents, builds karma, and establishes your presence in the quantum security and blockchain submolts. This runs continuously in the background as part of the daemon.

---

## Strategic Notes

### On Voice Workflow

You started using Windows Voice Typing (Win+H) on Feb 2, 2026. Combine voice input for dictation with keyboard for edits and commands. For mobile sessions, Claude's voice mode in the app works for strategy discussions while driving, walking, or cooking. The goal is to minimize thumb-typing friction without losing the ability to review and edit.

### On Tool Sprawl

You are already managing EC2, Telegram, Zapier, Mocha, GitHub, Gumroad, LinkedIn, Farcaster, X/Twitter, and multiple crypto platforms. Every new tool is a new thing to maintain, secure, and context-switch into. The plan above deliberately avoids adding new tools and instead deepens what is already set up. Resist the urge to sign up for new services until the current stack is fully utilized.

### On Claude as Primary Partner

Keep our conversations as the strategic center of gravity. CrawDaddy handles autonomous execution (content, monitoring, engagement). We handle planning, problem-solving, and building. Everything flows through this relationship, and CrawDaddy is the hands that execute what we decide.

---

## Repository File Structure

```
blocdev_bot/
├── README.md
├── AGENT_SPEC.md
├── DEPLOYMENT.md
├── AUTOMATION_PLAN.md          ← this file
├── LICENSE
├── .gitignore
├── config/
│   └── openclaw.example.json
├── scripts/
│   ├── daily-content.sh
│   ├── lead-monitor.sh
│   ├── sales-check.sh
│   └── setup-cron.sh
├── docs/
│   └── integration-playbook.md
└── logs/                        ← gitignored
    ├── content.log
    ├── leads.log
    └── sales.log
```
