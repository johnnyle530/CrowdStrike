# CrowdStrike-webhook
A Python webhook service that routes CrowdStrike detection alerts to different Slack channels based on severity and includes key context like host, user, and detection details.
# CrowdStrike to Slack Alert Router

## What this does
This project runs a lightweight webhook listener that receives CrowdStrike detection events and sends a formatted Slack alert to the correct channel based on severity.

Example routing:
- Critical → #security-incidents
- High → #it-alerts
- Medium → #it-triage
- Low → #it-low-priority

This helps IT and Security teams catch high-risk activity quickly without constantly monitoring the Falcon console.

## Features
- Receives CrowdStrike webhook events over HTTP
- Normalizes severity into Low, Medium, High, Critical
- Routes to specific Slack channels based on severity
- Supports a minimum severity threshold (optional)
- Adds useful context in Slack alerts (host, user, detection summary, timestamp)

## Architecture
CrowdStrike Webhook → Python Flask Service → Slack chat.postMessage

## Setup

### 1) Create a Slack App
You need a Slack bot token so the script can post to channels.

Required scope:
- chat:write

Then install the app to your workspace and copy the Bot User OAuth Token (starts with xoxb).

### 2) Get channel IDs
Channel IDs look like: C0123ABCDEF
You can copy the link to a channel in Slack and extract the ID, or use Slack tools to view channel details.

### 3) Configure environment variables
Create a .env file (do not commit it) and set:

- SLACK_BOT_TOKEN
- SLACK_CHANNEL_CRITICAL
- SLACK_CHANNEL_HIGH
- SLACK_CHANNEL_MEDIUM
- SLACK_CHANNEL_LOW

Optional:
- ALERT_MIN_SEVERITY (Low, Medium, High, Critical)
- WEBHOOK_SHARED_SECRET (simple request gate)

See .env.example for a template.

### 4) Install dependencies
```bash
pip3 install -r requirements.txt
