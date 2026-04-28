# Copilot Studio Topics — Import Guide

This folder contains the topic YAML files for each agent. These define the conversational flows, trigger phrases, and actions for the Copilot Studio agents.

## Topic Inventory

### RFP Creator Agent (`agents/rfp-creator/topics/`)

| Topic | File | Trigger | Purpose |
|-------|------|---------|---------|
| Greeting | `greeting.yaml` | "Hello", "Start", "RFP Creator" | Welcome message with engagement type overview |
| Create RFP | `create-rfp.yaml` | "Create RFP", "New RFP", "Draft an RFP" | Full RFP creation flow — collects details, calls Power Automate |
| Fallback | `fallback.yaml` | Unknown intent | AI-powered Q&A using knowledge sources |

### RFP Issuer Agent (`agents/rfp-issuer/topics/`)

| Topic | File | Trigger | Purpose |
|-------|------|---------|---------|
| Greeting | `greeting.yaml` | "Hello", "Start", "RFP Issuer" | Welcome message with available actions |
| Issue RFP | `issue-rfp.yaml` | "Issue RFP", "Send RFP" | Collects partner details, sends emails via Power Automate |
| Check Status | `check-status.yaml` | "Check status", "Track RFP" | Retrieves RFP tracking data from SharePoint |
| Fallback | `fallback.yaml` | Unknown intent | AI-powered Q&A using knowledge sources |

### RFP Evaluator Agent (`agents/rfp-evaluator/topics/`)

| Topic | File | Trigger | Purpose |
|-------|------|---------|---------|
| Greeting | `greeting.yaml` | "Hello", "Start", "Evaluate" | Welcome message with evaluation options |
| Evaluate Responses | `evaluate-responses.yaml` | "Evaluate responses", "Score" | AI-powered evaluation with scoring matrix |
| Generate Report | `generate-report.yaml` | "Generate report", "Final report" | Creates evaluation report via Power Automate |
| Fallback | `fallback.yaml` | Unknown intent | AI-powered Q&A using knowledge sources |

## How to Import Topics into Copilot Studio

### Option A: Copy-Paste YAML (Recommended)

1. Open [Copilot Studio](https://copilotstudio.microsoft.com)
2. Select your agent (e.g., "Kuok RFP Creator")
3. Go to **Topics** → **+ New topic** → **Create from blank**
4. Click the **Code editor** toggle (top-right of topic editor)
5. **Paste** the entire YAML content from the topic file
6. Switch back to visual editor — the topic will render with all nodes
7. Review and configure any `flowId:` references (link to your imported Power Automate flows)
8. **Save** and **Publish** the topic

### Option B: Manually Recreate

If the code editor is unavailable:

1. Use the YAML as a blueprint
2. Add each node manually in the visual editor:
   - `Question` nodes → "Ask a question" 
   - `SendActivity` nodes → "Send a message"
   - `ConditionGroup` nodes → "Add a condition"
   - `InvokeFlowAction` nodes → "Call an action" → select your Power Automate flow
   - `SearchAndSummarizeContent` nodes → "Generative answers"

## Knowledge Sources Setup

The fallback topics use `SearchAndSummarizeContent` which requires knowledge sources:

1. In Copilot Studio, go to **Knowledge** → **+ Add knowledge**
2. Add these SharePoint document libraries:
   - `/RFP Templates/` — Template documents
   - `/Knowledge Base/` — Evaluation guidelines and standard terms
3. Add the uploaded knowledge base files from this repo:
   - `knowledge-base/evaluation-guidelines/evaluation-framework.md`
   - `knowledge-base/standard-terms/standard-terms.md`

## Topic Configuration Checklist

After importing all topics, verify:

- [ ] All `flowId:` references point to the correct Power Automate flows
- [ ] Knowledge sources are connected for fallback topics
- [ ] Trigger phrases don't conflict between topics
- [ ] Variables are properly scoped (Topic-level)
- [ ] Agent is published after all topics are configured
