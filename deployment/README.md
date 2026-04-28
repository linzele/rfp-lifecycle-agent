# Deployment Guide

## Overview

Deploying the Kuok Group RFP Lifecycle Agent involves 6 key setup areas:

1. SharePoint Setup (document libraries and lists)
2. Dataverse Setup (structured knowledge tables)
3. Copilot Agent Setup (3 agents in Copilot Studio)
4. Knowledge Base Configuration (SharePoint files + Dataverse tables)
5. Power Automate Flow Setup (6 flows with approval step)
6. Microsoft Teams Setup (Deal Room channel + agent publishing)

---

## Prerequisites

- [Power Platform environment](https://learn.microsoft.com/en-us/power-platform/admin/create-environment) with System Administrator access
- Copilot Studio license
- Permission to run and edit Power Automate flows
- Access to create a SharePoint site
- Microsoft Teams access (or permissions to create a new Teams channel)
- Outlook access

---

## Step 1: SharePoint Setup

### 1.1 Create SharePoint Site
1. Go to `https://<your-tenant>.sharepoint.com/` → **Create site**
2. Select **Team Site** → **Standard Team** template
3. Name: `Kuok RFP Management` (or as desired)
4. **Save the URL** — you'll need it for flow configuration

### 1.2 Create Document Libraries
Create the following document libraries:

| Library Name | Purpose |
|-------------|---------|
| `RFP Templates` | Stores Word templates for each engagement type |
| `Issued RFPs` | Stores generated RFP documents |
| `Approved RFP Packages` | Stores approved RFPs ready for vendor distribution |
| `Partner Responses` | Stores incoming partner proposals |
| `Evaluation Reports` | Stores generated evaluation reports |
| `Knowledge Base` | Stores sample RFPs and reference documents |
| `Vendor Distribution List` | Stores vendor contact lists and distribution groups per RFP |

### 1.3 Create SharePoint Lists
Create the tracking lists defined in [Flow Definitions](../flows/flow-definitions.md#sharepoint-lists-required):
- **RFP Tracking List**
- **Partner Tracking List**

### 1.4 Upload Templates
Upload the Word versions of the RFP templates from the `templates/` folder to the `RFP Templates` library.

---

## Step 1.5: Dataverse Setup

The RFP Creator Agent uses Dataverse tables as structured knowledge sources for procurement templates, compliance criteria, and vendor evaluation data.

### 1.5.1 Create Dataverse Tables

In [Power Apps Maker Portal](https://make.powerapps.com) → **Tables** → **New table**, create the following:

| Table Name | Description | Key Columns |
|-----------|-------------|-------------|
| `Procurement Template` | Reusable procurement templates and clauses | Name, Category (Turnkey/Managed Services/Augmented Resources), Template Content, Version, Status (Active/Archived) |
| `Compliance Criteria` | Regulatory and internal compliance requirements | Criteria Name, Category, Description, Mandatory (Yes/No), Applicable Engagement Types |
| `Vendor Evaluation DB` | Historical vendor performance and evaluation records | Vendor Name, Engagement Type, Overall Score, Strengths, Weaknesses, Last Evaluated Date |
| `Project Spec Library` | Project specifications and technical requirements | Spec Name, Project Type, Description, Technical Requirements, Attachments |

### 1.5.2 Populate Initial Data

1. Open each table in Power Apps
2. Add initial rows based on your organisation's existing data:
   - **Procurement Templates** — Import standard clauses from existing RFP documents
   - **Compliance Criteria** — Add regulatory requirements (e.g., data residency, security standards)
   - **Vendor Evaluation DB** — Import historical vendor scorecards if available
   - **Project Spec Library** — Add common project specifications and technical standards

### 1.5.3 Connect Dataverse to Copilot Studio

For each agent that needs Dataverse knowledge:
1. In Copilot Studio, go to **Knowledge** → **Add knowledge**
2. Select **Dataverse** as the source
3. Choose the relevant tables (e.g., RFP Creator needs all 4 tables)
4. Wait for indexing to complete

---

## Step 2: Copilot Agent Setup

### 2.1 Create Agents in Copilot Studio
1. Navigate to [Copilot Studio](https://copilotstudio.microsoft.com)
2. Select your environment
3. Create three new agents (or use existing ones)

### 2.2 Configure Agents
Three agents are required:

| Agent | Purpose | Instructions |
|-------|---------|-------------|
| RFP Creator | Creates structured RFPs | [agents/rfp-creator/instructions.md](../agents/rfp-creator/instructions.md) |
| RFP Issuer | Distributes RFPs to partners | [agents/rfp-issuer/instructions.md](../agents/rfp-issuer/instructions.md) |
| RFP Evaluator | Evaluates partner responses | [agents/rfp-evaluator/instructions.md](../agents/rfp-evaluator/instructions.md) |

For each agent in Copilot Studio:
1. Open the agent
2. Go to **Settings** → **Instructions**
3. Paste the instructions from the corresponding `instructions.md` file
4. **Save** and **Publish**

### 2.3 Enable Deep Reasoning
For the **RFP Evaluator Agent**:
1. Navigate to **Settings** → **Generative AI**
2. Enable **Use deep reasoning models**
3. Click **Save**

---

## Step 3: Knowledge Base

### 3.1 Upload Knowledge Documents (SharePoint)
In Copilot Studio, for each agent:
1. Go to the **Knowledge** tab
2. Click **Add knowledge** → **Upload Files**
3. Upload the relevant documents:

| Agent | Knowledge Sources |
|-------|------------------|
| RFP Creator | Standard terms, RFP templates, sample RFPs |
| RFP Evaluator | Evaluation framework, sample evaluation reports, standard terms |
| RFP Issuer | Email templates (auto-configured) |

4. Wait for indexing to complete (approximately 10–15 minutes per batch)

### 3.2 Connect Dataverse Knowledge
For the **RFP Creator Agent** (primary Dataverse consumer):
1. Go to **Knowledge** → **Add knowledge** → **Dataverse**
2. Select: `Procurement Template`, `Compliance Criteria`, `Vendor Evaluation DB`, `Project Spec Library`
3. This gives the agent access to RFP, compliance, security data, and project plan docs

For the **RFP Evaluator Agent**:
1. Add **Dataverse** knowledge: `Vendor Evaluation DB`, `Compliance Criteria`
2. This enables the evaluator to cross-reference historical vendor performance

### 3.3 Connect SharePoint Knowledge (Optional)
Optionally, connect the SharePoint `Knowledge Base` library as a knowledge source so agents can reference new documents added over time without manual re-upload.

---

## Step 4: Power Automate Flows

Six flows are defined in this solution. See [Flow Definitions](../flows/flow-definitions.md) for complete specifications and the [Visual Build Guide](../docs/guides/all-flows-guide.html) for step-by-step instructions.

### 4.1 Build Each Flow
Follow the **[All Flows Build Guide](../docs/guides/all-flows-guide.html)** to create each flow in Power Automate. The guide provides:
- Numbered steps with exact field values for each action
- Configuration tables for SharePoint URLs, Teams channels, and connectors
- Flow diagrams showing the action sequence

After building, update these values in each flow:
- **SharePoint Site URL** → Your site from Step 1.1
- **Document Library paths** → Match the library names from Step 1.2 (including `Approved RFP Packages` and `Vendor Distribution List`)
- **Email addresses** → RFP monitoring mailbox
- **Teams channel** → Deal Room channel from Step 5
- **Dataverse table names** → Match the tables from Step 1.5

### 4.2 Configure the Approval Step
The RFP Creation flow (Flow 1) should include an approval step before the final RFP is distributed to vendors:

1. In Flow 1, after the RFP document is generated, add an **Approvals - Start and wait for an approval** action
2. Set **Approval type** → Approve/Reject - First to respond
3. Set **Assigned to** → The procurement manager or approver email
4. Set **Details** → Include the RFP reference number and link to the generated document
5. Add a **Condition** after the approval:
   - If **Approved** → Copy the document to `Approved RFP Packages` library, then proceed to distribution
   - If **Rejected** → Post a Teams notification with rejection comments, return to draft status

### 4.3 Ensure All Flows Are Turned On
In the Maker Portal → **Flows**, verify all 6 flows show **Status: On**.

---

## Step 5: Teams Setup

### 5.1 Create Teams Channel
1. In Microsoft Teams, select the appropriate Team
2. Create a channel named **Deal Room** (or as desired)
3. This channel receives all RFP notifications, draft proposals, and evaluation results

### 5.2 Publish Agents to Teams
For each agent in Copilot Studio:
1. Go to **Channels** → **Teams + Microsoft 365**
2. Check **Make agent available in M365 Copilot Chat** (if you have M365 Copilot licenses)
3. Click **Add channel** → **Save**
4. Click **Publish**

---

## Verification

After deployment, verify the end-to-end flow:

1. ✅ Open Teams → interact with the RFP Creator Agent → generate a test RFP
2. ✅ Verify the RFP document appears in the SharePoint `Issued RFPs` library
3. ✅ Use the RFP Issuer Agent to send a test issuance (to yourself)
4. ✅ Reply with a mock response → verify it appears in `Partner Responses`
5. ✅ Use the RFP Evaluator Agent to evaluate the mock response
6. ✅ Verify the evaluation report appears in `Evaluation Reports`
7. ✅ Verify Teams Deal Room receives all notifications

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Agent not appearing in Teams | Ensure the agent is published and the Teams channel is configured |
| Flows not triggering | Check flow status is **On** and connections are authenticated |
| Knowledge not indexed | Wait 15 minutes after upload; check status in Copilot Studio Knowledge tab |
| SharePoint permissions error | Ensure the flow connection account has Contribute access to all libraries |
| Email trigger not working | Verify the monitored email address and subject filter are correct |
