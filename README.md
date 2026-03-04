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

--------------------------------------------------------------
Using Slack Webhooks + CrowdStrike Fusion Workflows.

Step 1. Create a Slack channel for security alerts

First you want a place where alerts will go.

Example channel name:

#security-alerts

or

#soc-alerts

This keeps security notifications separate from normal IT chatter.

Typical alerts you want here:

Malware detection

Suspicious behavior

Endpoint containment events

High severity threats

Step 2. Create a Slack Incoming Webhook

Slack needs a way for external systems to send messages into the channel.

Think of the webhook as a secure URL that accepts alerts.

Steps in Slack:

Go to Slack App Directory

Search for Incoming Webhooks

Click Add to Slack

Choose the channel (ex: #security-alerts)

Slack will generate a URL like:

https://hooks.slack.com/services/TXXXX/BXXXX/XXXX

Save this URL.
This is what CrowdStrike will use to send alerts.

Step 3. Go to CrowdStrike Falcon console

Login to:

CrowdStrike Falcon Dashboard

Then go to:

Platform → Falcon Fusion Workflows

Falcon Fusion is CrowdStrike’s automation engine.

This is where you build workflows like:

If detection occurs → send Slack alert
Step 4. Create a new workflow

Click:

Create Workflow

Name it something clear:

Send Security Alerts to Slack

Now we configure the automation.

Step 5. Define the trigger

The trigger is what event starts the automation.

Choose:

Trigger Type

Detection Created

This means whenever CrowdStrike detects a threat on a device.

Step 6. Filter by severity

You probably don’t want every small alert going to Slack.

Add a condition:

Severity = Medium OR High OR Critical

Example logic:

IF Detection Severity >= Medium

This prevents low noise alerts.

Step 7. Add the action (Send to Slack)

Now define what happens when the trigger fires.

Choose:

Action → Webhook

Paste the Slack webhook URL you created earlier.

Step 8. Format the alert message

You want the message to include useful security information.

Example message format:

Security Alert from CrowdStrike

Severity: High
Host: MacBook-Johnny
User: johnny.le
Threat: Credential Dumping

View Detection:
https://falcon.crowdstrike.com/detections

This helps the security team immediately understand:

which machine

which user

what type of threat

where to investigate

Step 9. Test the workflow

Before going live, test it.

You can:

Trigger a test detection

Or use a workflow test option

Then confirm Slack receives something like:

CrowdStrike Detection Alert

Severity: High
Host: macbook-23
User: jsmith
Threat: Malware Execution

If the message appears in Slack, the automation works.

Step 10. Activate the workflow

Finally:

Click Enable Workflow

Now CrowdStrike will automatically send Slack alerts whenever:

Medium
High
Critical
detections occur
