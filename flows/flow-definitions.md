# Power Automate Flow Definitions

This document defines the Power Automate flows required for the RFP Lifecycle Agent solution.

---

## Flow 1: RFP Creation — Populate Template

**Trigger:** Manual (invoked by RFP Creator Agent via Copilot Studio action)

**Steps:**
1. Receive structured RFP data from Copilot Studio (JSON payload)
2. Select the correct Word template from SharePoint based on engagement type
3. Populate the Word template with the RFP data
4. Save the generated RFP document to SharePoint: `/Issued RFPs/[RFP Reference]/`
5. Return the SharePoint document URL to Copilot Studio
6. Post a notification in the Teams Deal Room channel with an Adaptive Card

**Input Parameters:**
- `rfpReference` (string)
- `engagementType` (string: Turnkey / ManagedServices / AugmentedResources)
- `projectName` (string)
- `rfpContent` (JSON object with all RFP sections)

---

## Flow 2: RFP Issuance — Send to Partners

**Trigger:** Manual (invoked by RFP Issuer Agent via Copilot Studio action)

**Steps:**
1. Receive partner list and RFP document reference from Copilot Studio
2. For each partner:
   a. Compose the cover email using the standard template
   b. Attach the RFP document from SharePoint
   c. Send the email via Outlook
   d. Log the issuance in the tracking SharePoint list
3. Post a summary in Teams Deal Room: "RFP [Reference] issued to [N] partners"

**Input Parameters:**
- `rfpReference` (string)
- `rfpDocumentUrl` (string — SharePoint URL)
- `partners` (array of objects: name, company, email)
- `submissionDeadline` (datetime)
- `qaDeadline` (datetime)
- `contactPerson` (object: name, email)

---

## Flow 3: Response Collection — Monitor Incoming Emails

**Trigger:** Automated — When a new email arrives (Outlook connector)

**Filter Conditions:**
- Subject contains the RFP Reference pattern: `KG-RFP-`
- Has attachments: Yes

**Steps:**
1. Extract RFP reference from email subject
2. Identify the partner from the sender email address (match against tracking list)
3. For each attachment:
   a. Save to SharePoint: `/Partner Responses/[RFP Reference]/[Partner Company]/`
4. Update the tracking SharePoint list: mark response as received with timestamp
5. Post notification in Teams Deal Room: "Response received from [Partner] for [RFP Reference]"
6. If all expected responses received, notify the RFP contact person

---

## Flow 4: Reminder — Approaching Deadline

**Trigger:** Scheduled — Runs daily at 9:00 AM SGT

**Steps:**
1. Query the tracking SharePoint list for RFPs with:
   - Submission deadline within 7 days AND responses still pending
   - Submission deadline within 2 days AND responses still pending
2. For each pending partner:
   - If 7 days out: Send courtesy reminder email
   - If 2 days out: Send final reminder email
3. Log reminder sent in the tracking list
4. Post summary in Teams Deal Room: "[N] reminders sent for [RFP Reference]"

---

## Flow 5: Evaluation Report — Generate Document

**Trigger:** Manual (invoked by RFP Evaluator Agent via Copilot Studio action)

**Steps:**
1. Receive evaluation data from Copilot Studio (JSON payload)
2. Populate the evaluation report Word template
3. Generate comparison charts (via Office Scripts or Excel):
   - Radar chart of scores per partner
   - Bar chart of weighted total scores
   - Commercial comparison table
4. Save the report to SharePoint: `/Evaluation Reports/[RFP Reference]/`
5. Post the report in Teams Deal Room as an Adaptive Card with:
   - Recommendation summary
   - Link to full report
   - "Approve" / "Request Changes" buttons

---

## Flow 6: Post-Deadline Summary

**Trigger:** Automated — When submission deadline passes (based on tracking list date)

**Steps:**
1. Query tracking list for the RFP
2. Compile summary: partners invited, responses received, missing
3. Post summary Adaptive Card in Teams Deal Room
4. If all responses received: notify evaluators that evaluation can begin
5. If responses missing: notify RFP contact person

---

## SharePoint Lists Required

### RFP Tracking List
| Column | Type | Description |
|--------|------|-------------|
| RFPReference | Text | Unique RFP identifier |
| ProjectName | Text | Project name |
| EngagementType | Choice | Turnkey / ManagedServices / AugmentedResources |
| Status | Choice | Draft / Issued / Collecting / Evaluating / Completed |
| SubmissionDeadline | DateTime | Partner response deadline |
| IssuedDate | DateTime | When the RFP was sent |
| ContactPerson | Person | RFP owner |

### Partner Tracking List
| Column | Type | Description |
|--------|------|-------------|
| RFPReference | Lookup | Link to RFP Tracking List |
| PartnerName | Text | Contact name |
| CompanyName | Text | Company name |
| Email | Text | Partner email |
| EmailSent | DateTime | When RFP was sent |
| Acknowledged | Yes/No | Partner acknowledged receipt |
| ResponseReceived | Yes/No | Response submitted |
| ResponseDate | DateTime | When response was received |
| RemindersSent | Number | Count of reminders sent |
