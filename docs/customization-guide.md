# Customization Guide

## Overview
This solution is designed to be easily extended. All configuration and customisation can be done in Copilot Studio and Power Platform without coding.

---

## Customising Agent Instructions

### Adding New Engagement Types
1. Create a new RFP template in the `templates/` folder
2. Update the RFP Creator Agent instructions to include the new type:
   - Add the type to the engagement type definitions
   - Define the standard requirements checklist
   - Update the evaluation criteria defaults
3. Create a new Word template and upload to SharePoint `RFP Templates` library
4. Update Power Automate flows to recognise the new type

### Modifying Evaluation Criteria
1. Edit `knowledge-base/evaluation-guidelines/evaluation-framework.md`
2. Update the default criteria tables for the relevant engagement type
3. Re-upload to Copilot Studio knowledge sources
4. The RFP Creator Agent will use the new defaults when generating RFPs

### Changing Standard Terms
1. Edit `knowledge-base/standard-terms/standard-terms.md`
2. Have Kuok Group Legal review any changes
3. Re-upload to Copilot Studio knowledge sources

---

## Adding Knowledge Sources

### Past RFPs
Upload anonymised past RFPs to `knowledge-base/sample-rfps/` and add them as knowledge sources in Copilot Studio. The more examples the agents have, the better their output quality.

### Industry-Specific Templates
Add industry-specific requirements or compliance documents to the knowledge base for specialised procurements (e.g., financial services, healthcare).

---

## Extending Power Automate Flows

### Adding Approval Workflows
Add a Power Automate approval step before the RFP Issuer sends to partners:
1. After the RFP Creator generates the document
2. Route for approval to the designated approver
3. Only proceed to issuance after approval

### Adding External Integrations
- **CRM Integration**: Log RFP activities in Dynamics 365 or Salesforce
- **Vendor Management**: Pull partner lists from a vendor management system
- **Document Signing**: Integrate with DocuSign or Adobe Sign for contract execution

### Adding Multi-Language Support
1. Create translated versions of the RFP templates
2. Update agent instructions to support language selection
3. Add translated standard terms to the knowledge base

---

## Copilot Studio Capabilities to Explore

| Capability | Use Case |
|-----------|----------|
| [Autonomous Triggers](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-triggers-about) | Auto-start RFP creation when a specific email pattern is detected |
| [Deep Reasoning](https://www.youtube.com/watch?v=_v9ri9eoVFg) | Complex evaluation analysis and recommendation generation |
| [Adaptive Cards](https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/adaptive-cards-overview) | Rich interactive notifications in Teams |
| [M365 Copilot Integration](https://learn.microsoft.com/en-us/microsoft-copilot-studio/) | Use agents directly in Word to edit RFP documents with AI assistance |

---

## References

- [Microsoft RFP Response Accelerator](https://github.com/microsoft/agent-for-rfp-response-solution-accelerator)
- [Awesome Copilot Studio Agents](https://github.com/kesslernity/awesome-copilot-studio-agents)
- [Copilot Studio Documentation](https://learn.microsoft.com/en-us/microsoft-copilot-studio/)
- [Power Automate Documentation](https://learn.microsoft.com/en-us/power-automate/)
