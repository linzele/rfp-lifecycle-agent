# Power Automate Flows — Setup Guide

This folder contains 6 Power Automate flow definitions as JSON reference files. These are **logic references**, not direct import packages. Create your flows from within Copilot Studio, then use these JSONs to build the action logic.

## Flow Inventory

| # | File | Trigger | Description |
|---|------|---------|-------------|
| 1 | `flow-01-rfp-creation.json` | Copilot Studio | Populates Word template, saves to SharePoint, posts to Teams |
| 2 | `flow-02-rfp-issuance.json` | Copilot Studio | Sends RFP emails to partners, logs tracking entries |
| 3 | `flow-03-response-collection.json` | Automated (new email) | Monitors inbox for RFP responses, saves attachments |
| 4 | `flow-04-deadline-reminder.json` | Scheduled (daily 9 AM SGT) | Sends courtesy/final reminders to pending partners |
| 5 | `flow-05-evaluation-report.json` | Copilot Studio | Generates evaluation report, posts approval card |
| 6 | `flow-06-post-deadline-summary.json` | Scheduled (daily 10 AM SGT) | Posts response summary after deadline passes |

## How to Create Flows (Recommended)

### Copilot Studio-Triggered Flows (Flows 1, 2, 5)

These flows are called from topic YAML files via `InvokeFlowAction`. Create them from Copilot Studio, then paste the definition via Code View.

1. Open your agent in [Copilot Studio](https://copilotstudio.microsoft.com)
2. Open the topic that calls this flow (e.g., `create-rfp` topic for Flow 1)
3. In the topic editor, click the **Call an action** node (+) and select **Create a new flow**
4. Power Automate opens — click the **Code** toggle (top-right of designer)
5. **Select all** the existing JSON and **replace** it with the contents of the corresponding flow JSON file
6. Click the **Code** toggle again to switch back to Designer view — verify the actions loaded correctly
7. Configure your SharePoint site URL, Teams channel, and connector connections
8. **Save** the flow — it auto-registers with Copilot Studio
9. Back in Copilot Studio, the flow appears in the action dropdown — select it
10. Map the input/output variables to your topic variables

### Standalone Flows (Flows 3, 4, 6)

These flows run independently (email triggers or daily schedules). Create them in Power Automate, then paste via Code View.

1. Go to [make.powerautomate.com](https://make.powerautomate.com)
2. Select your environment (must match Copilot Studio environment)
3. Click **+ Create** and select the trigger type
   - Flow 3: **Automated cloud flow** with "When a new email arrives" trigger
   - Flow 4: **Scheduled cloud flow** with daily recurrence at 9 AM SGT
   - Flow 6: **Scheduled cloud flow** with daily recurrence at 10 AM SGT
4. Once in the designer, click the **Code** toggle (top-right)
5. **Replace** the JSON with the contents of the corresponding flow JSON file
6. Switch back to Designer view, configure connections and save

### Step-by-Step: Reading the JSON Reference

Each JSON file has this structure. Use it as a blueprint for adding actions in the designer.

```
definition.triggers   → Already set up when you create the flow
definition.actions    → Each key is one action to add in the designer
  ├── Type            → The Power Automate action type
  ├── inputs          → The parameters to configure
  └── runAfter        → Which action it follows (determines order)
```

**Example** (from flow-01-rfp-creation.json):
- `Initialize_RFP_Reference` → Add "Initialize variable" action, set name to rfpReference
- `Switch_Engagement_Type` → Add "Switch" action on engagementType
- `Populate_Word_Template` → Add "Populate a Word template" action
- `Create_File_In_SharePoint` → Add "Create file" SharePoint action
- `Post_Teams_Notification` → Add "Post message in a chat or channel" Teams action
- `Respond_to_Copilot_Studio` → Add "Respond to Copilot" action with output variables

## Configuration Values

Replace these placeholders when building your flows:

| Placeholder | Replace With |
|-------------|-------------|
| `CONFIGURE_SHAREPOINT_SITE_URL` | Your SharePoint site URL (e.g., `https://kuokgroup.sharepoint.com/sites/IT-RFP`) |
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
