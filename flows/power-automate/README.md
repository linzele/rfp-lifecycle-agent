# Power Automate Flows — Setup Guide

This folder contains 6 Power Automate flow definitions as JSON reference files. These describe the **logic and action sequence** for each flow — use them as blueprints when building flows in the Power Automate designer.

> **Visual step-by-step guide:** Open the [All Flows Build Guide](https://linzele.github.io/rfp-lifecycle-agent/flows/power-automate/all-flows-guide.html) for a detailed, visual walkthrough of building each flow with screenshots-style instructions, config tables, and prerequisite callouts.

## Flow Inventory

| # | File | Trigger | Description |
|---|------|---------|-------------|
| 1 | `flow-01-rfp-creation.json` | Copilot Studio | Populates Word template, saves to SharePoint, posts to Teams |
| 2 | `flow-02-rfp-issuance.json` | Copilot Studio | Sends RFP emails to partners, logs tracking entries |
| 3 | `flow-03-response-collection.json` | Automated (new email) | Monitors inbox for RFP responses, saves attachments |
| 4 | `flow-04-deadline-reminder.json` | Scheduled (daily 9 AM SGT) | Sends courtesy/final reminders to pending partners |
| 5 | `flow-05-evaluation-report.json` | Copilot Studio | Generates evaluation report, posts approval card |
| 6 | `flow-06-post-deadline-summary.json` | Scheduled (daily 10 AM SGT) | Posts response summary after deadline passes |

## How to Build the Flows

### Recommended: Follow the Visual Guide

The easiest way to build all 6 flows is to follow the **[All Flows Build Guide](../docs/guides/all-flows-guide.html)** — a single HTML page with:

- Sidebar navigation to jump between flows
- Numbered step-by-step instructions for each action
- Configuration tables showing exactly what to enter in each field
- Prerequisite callouts for SharePoint lists and Word templates
- Flow diagrams showing the action sequence

Open the HTML file in your browser from the `docs/guides/` folder, or view it on GitHub Pages.

### Copilot Studio-Triggered Flows (Flows 1, 2, 5)

These flows are called from Copilot Studio agent topics. Create them from within Copilot Studio:

1. Open your agent in [Copilot Studio](https://copilotstudio.microsoft.com)
2. Open the topic that calls this flow (e.g., `create-rfp` topic for Flow 1)
3. Click the **Call an action** node (+) and select **Create a new flow**
4. Power Automate opens — follow the [Build Guide](../docs/guides/all-flows-guide.html) to add each action
5. Configure your SharePoint site URL, Teams channel, and connector connections
6. **Save** the flow — it auto-registers with Copilot Studio

### Standalone Flows (Flows 3, 4, 6)

These flows run independently (email triggers or daily schedules):

1. Go to [make.powerautomate.com](https://make.powerautomate.com)
2. Select your environment (must match Copilot Studio environment)
3. Click **+ Create** and select the trigger type:
   - Flow 3: **Automated cloud flow** → "When a new email arrives" trigger
   - Flow 4: **Scheduled cloud flow** → Daily recurrence at 9 AM SGT
   - Flow 6: **Scheduled cloud flow** → Daily recurrence at 10 AM SGT
4. Follow the [Build Guide](../docs/guides/all-flows-guide.html) for the corresponding flow section

### Using the JSON Files as Reference

Each JSON file describes the complete flow logic. Use it as a blueprint:

```
definition.triggers   → Already set up when you create the flow
definition.actions    → Each key is one action to add in the designer
  ├── Type            → The Power Automate action type
  ├── inputs          → The parameters to configure
  └── runAfter        → Which action it follows (determines order)
```

## Configuration Values

Replace these placeholders when building your flows:

| Placeholder | Replace With |
|-------------|-------------|
| `CONFIGURE_SHAREPOINT_SITE_URL` | Your SharePoint site URL (e.g., `https://yourtenant.sharepoint.com/sites/IT-RFP`) |
| `CONFIGURE_DOCUMENT_LIBRARY_ID` | The document library GUID from SharePoint |
| `CONFIGURE_TEAMS_CHANNEL_ID` | The Teams Deal Room channel ID |

## Required Connectors

Ensure these connectors are available in your Power Platform environment:

| Connector | Used By Flows |
|-----------|--------------|
| SharePoint | All flows |
| Office 365 Outlook | Flows 2, 3, 4 |
| Microsoft Teams | All flows |
| Word Online (Business) | Flows 1, 5 |

## Linking Flows to Topic YAML Files

After creating the flows from Copilot Studio, update the `flowId` in each topic YAML:

1. In Power Automate, open the flow and copy its flow ID from the URL (the GUID)
2. In the topic YAML files, replace `00000000-0000-0000-0000-000000000000` with the real GUID

| Agent | Topic File | Flow |
|-------|-----------|------|
| RFP Creator | `create-rfp.yaml` | Flow 1 — RFP Creation |
| RFP Issuer | `issue-rfp.yaml` | Flow 2 — RFP Issuance |
| RFP Issuer | `check-status.yaml` | Flow 3 — Response Collection |
| RFP Evaluator | `evaluate-responses.yaml` | Flow 3 — Response Collection |
| RFP Evaluator | `generate-report.yaml` | Flow 5 — Evaluation Report |
