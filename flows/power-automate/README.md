# Power Automate Flows — Import Guide

This folder contains 6 Power Automate cloud flow definitions in JSON format, ready to import into your Power Platform environment.

## Flow Inventory

| # | File | Trigger | Description |
|---|------|---------|-------------|
| 1 | `flow-01-rfp-creation.json` | Manual (Copilot Studio) | Populates Word template, saves to SharePoint, posts to Teams |
| 2 | `flow-02-rfp-issuance.json` | Manual (Copilot Studio) | Sends RFP emails to partners, logs tracking entries |
| 3 | `flow-03-response-collection.json` | Automated (new email) | Monitors inbox for RFP responses, saves attachments |
| 4 | `flow-04-deadline-reminder.json` | Scheduled (daily 9 AM SGT) | Sends courtesy/final reminders to pending partners |
| 5 | `flow-05-evaluation-report.json` | Manual (Copilot Studio) | Generates evaluation report, posts approval card |
| 6 | `flow-06-post-deadline-summary.json` | Scheduled (daily 10 AM SGT) | Posts response summary after deadline passes |

## How to Import

### Option A: Import via Power Automate Portal

1. Go to [make.powerautomate.com](https://make.powerautomate.com)
2. Select your environment (must match Copilot Studio environment)
3. Click **My flows** → **Import** → **Import Package (Legacy)**
4. Upload each JSON file
5. Map the connection references to your environment's connections

### Option B: Paste into Copilot Studio Action

For flows triggered by Copilot Studio (Flows 1, 2, 5):

1. Open your agent in [Copilot Studio](https://copilotstudio.microsoft.com)
2. Navigate to **Actions** → **+ Add an action** → **Create a new flow**
3. This opens the Power Automate editor
4. Switch to **Code view** (toggle in top-right)
5. Replace the default JSON with the contents of the flow file
6. Switch back to **Designer view** to verify
7. Configure the connection references (see below)
8. **Save** the flow

### Option C: Power Platform CLI

```bash
# Install Power Platform CLI
npm install -g @microsoft/powerplatform-cli

# Authenticate
pac auth create --environment https://YOUR_ORG.crm.dynamics.com

# Import solution (if packaged)
pac solution import --path ./solution/KuokRFPLifecycle.zip
```

## Configuration Required

After importing, search for `CONFIGURE_` placeholders in each flow and replace them:

| Placeholder | Replace With |
|-------------|-------------|
| `CONFIGURE_SHAREPOINT_SITE_URL` | Your SharePoint site URL (e.g., `https://kuokgroup.sharepoint.com/sites/IT-RFP`) |
| `CONFIGURE_DOCUMENT_LIBRARY_ID` | The document library GUID from SharePoint |
| `CONFIGURE_TEAMS_CHANNEL_ID` | The Teams Deal Room channel ID |
| `CONFIGURE_LOOKUP_BY_RFP_REFERENCE` | Replace with a "Get Items" + filter pattern for your list |

## Connection References

Each flow uses these connectors — ensure they're available in your environment:

| Connector | Connection Reference Name | Used By |
|-----------|--------------------------|---------|
| SharePoint | `kg_sharepoint_connection` | All flows |
| Office 365 Outlook | `kg_outlook_connection` | Flows 2, 3, 4 |
| Microsoft Teams | `kg_teams_connection` | All flows |
| Word Online (Business) | `kg_word_connection` | Flows 1, 5 |

## Linking Flows to Copilot Studio Topics

After importing the flows, link them to the agent topics:

1. **RFP Creator Agent** → `create-rfp.yaml` → Link to **Flow 1** (RFP Creation)
2. **RFP Issuer Agent** → `issue-rfp.yaml` → Link to **Flow 2** (RFP Issuance)
3. **RFP Evaluator Agent** → `generate-report.yaml` → Link to **Flow 5** (Evaluation Report)

In each topic YAML, find the `flowId:` comment and replace with the actual flow ID from Power Automate.
