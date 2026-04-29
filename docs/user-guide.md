# User Guide — RFP Lifecycle Agent

## Getting Started

The RFP Lifecycle Agent consists of three AI agents available in Microsoft Teams. Each agent handles a specific phase of the RFP process.

---

## Agent 1: RFP Creator

### What It Does
Creates structured, standards-compliant RFPs based on your requirements. Supports three engagement types with AI-enriched requirements.

### How to Use
1. Open Microsoft Teams → find **RFP Creator Agent** in your chat or Apps
2. Start a conversation: *"I need to create an RFP for a managed services engagement"*
3. The agent will ask for details about your project — provide:
   - Engagement type
   - Project description and objectives
   - Scope of work
   - Technical requirements
   - Timeline and budget
4. The agent will:
   - Check your requirements against the standard checklist
   - Suggest additional requirements you may have missed
   - Ask you to confirm the final requirements list
5. Once confirmed, the agent generates the RFP document and saves it to SharePoint

### Tips
- Be as specific as possible with your requirements — the agent enriches, not invents
- Review the AI-suggested requirements carefully before confirming
- The generated RFP is a draft — review before issuing to partners

---

## Agent 2: RFP Issuer

### What It Does
Distributes approved RFPs to invited partners, tracks responses, and sends reminders.

### How to Use
1. Open **RFP Issuer Agent** in Teams
2. Say: *"Issue RFP KG-RFP-2026-001 to our partner list"*
3. Provide the partner list (names and email addresses)
4. Review the cover email draft → confirm to send
5. The agent tracks responses and sends automatic reminders

### What to Expect
- Each partner receives an individual email (not group email)
- Reminders are sent 7 days and 2 days before the deadline
- Response documents are automatically saved to SharePoint
- You'll receive Teams notifications when responses arrive

---

## Agent 3: RFP Evaluator

### What It Does
Evaluates partner responses against your criteria, compares commercial terms, and generates an evaluation report.

### How to Use
1. Open **RFP Evaluator Agent** in Teams
2. Say: *"Evaluate responses for RFP KG-RFP-2026-001"*
3. The agent pulls partner responses from SharePoint
4. It evaluates each response across all criteria:
   - Compliance check
   - Technical scoring
   - Commercial comparison
   - Risk assessment
5. A draft evaluation report is posted in the Deal Room
6. Your team reviews and provides feedback
7. The agent incorporates feedback and generates the final report

### Understanding the Evaluation Report
- **Scoring Matrix**: Side-by-side comparison of all partners
- **Compliance Summary**: Which partners meet all requirements
- **Commercial Comparison**: Normalised pricing comparison
- **Recommendation**: Evidence-based recommendation with rationale
- **Next Steps**: Required approvals and timeline

---

## The Deal Room (Teams Channel)

All RFP activity is centralised in the **Deal Room** Teams channel:
- 📄 New RFPs created
- 📨 RFPs issued to partners
- 📥 Partner responses received
- 📊 Evaluation reports posted
- ⏰ Deadline reminders and status updates

---

## SharePoint Document Structure

```
RFP Management/
├── RFP Templates/           ← Word templates per engagement type
├── Issued RFPs/
│   └── KG-RFP-2026-001/    ← Generated RFP documents
├── Partner Responses/
│   └── KG-RFP-2026-001/
│       ├── Acme Corp/       ← Partner A's response docs
│       └── Beta Ltd/        ← Partner B's response docs
├── Evaluation Reports/
│   └── KG-RFP-2026-001/    ← Evaluation report and scoresheets
└── Knowledge Base/          ← Reference documents for agents
```

---

## FAQ

**Q: Can I edit the generated RFP before issuing?**  
A: Yes. The RFP Creator generates a Word document in SharePoint. Edit it as needed, then use the RFP Issuer Agent to distribute the final version.

**Q: Can I add more partners after the RFP is issued?**  
A: Yes. Tell the RFP Issuer Agent: *"Add [Partner Name] to RFP KG-RFP-2026-001"*. The agent will issue the RFP to the additional partner.

**Q: How are evaluation scores calculated?**  
A: Each criterion is scored 0-5, then multiplied by its weight. The weighted scores are summed for a total. See the [Evaluation Framework](../knowledge-base/evaluation-guidelines/evaluation-framework.md) for details.

**Q: Can I change the evaluation criteria?**  
A: Yes. When starting an evaluation, tell the agent your custom criteria and weightings. Default criteria are applied only if you don't specify.
