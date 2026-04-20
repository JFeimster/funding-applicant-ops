# Setup Checklist

Use this checklist to stand up or rebuild the `funding-applicant-ops` system without guessing.

This is the operator runbook for getting the repo, n8n, Gmail, and HubSpot working together.

---

## Goal

At the end of this checklist, you should have:
- the repo files in place
- both n8n workflows imported
- Gmail connected
- HubSpot connected
- webhook wiring completed
- a successful test payload run

---

## 1. Repo Check

Confirm these files exist in the repo:

- `README.md`
- `agents.md`
- `prompts.md`
- `docs/field-mapping.md`
- `docs/task-sequence.md`
- `assets/sample-webhook-payloads.json`
- `workflows/hubspot-funding-applicant-workflow.json`
- `workflows/gmail-parser-funding-applicants.json`

If any are missing, fix the repo first before touching n8n.

---

## 2. n8n Environment Check

Confirm you can access your n8n instance and save workflows.

### Verify:
- n8n is running
- you can create credentials
- webhook URLs are reachable from the outside if needed
- timezone is correct in n8n

### Recommended:
- set the workflow timezone intentionally
- avoid building on a broken or half-configured n8n instance

---

## 3. Import Workflow 1 — HubSpot Intake

Import:
- `workflows/hubspot-funding-applicant-workflow.json`

### After import:
- open the workflow
- confirm the webhook node path is:
  - `funding-applicant-intake`
- save the workflow
- copy the webhook URL

Do not skip this. The Gmail parser depends on this URL.

---

## 4. Create HubSpot Credential in n8n

You have two practical options.

### Option A — HubSpot OAuth2
Use if your n8n version supports it cleanly and you want a native auth flow.

### Option B — HTTP Header Auth with HubSpot Private App Token
Usually the simplest option for HTTP Request nodes.

#### Steps:
1. In HubSpot, go to **Settings**
2. Search for **Private Apps**
3. Create a private app
4. Grant scopes needed for:
   - contacts
   - deals
   - tasks / CRM objects
5. Copy the token

#### In n8n:
6. Go to **Credentials**
7. Create new credential:
   - **HTTP Header Auth**
8. Set:
   - Header Name: `Authorization`
   - Header Value: `Bearer YOUR_PRIVATE_APP_TOKEN`
9. Save with a clear name like:
   - `HubSpot - Funding Ops`

### Then:
10. Open each HubSpot HTTP Request node in the imported workflow
11. Assign the saved credential
12. Save the workflow

---

## 5. Import Workflow 2 — Gmail Parser

Import:
- `workflows/gmail-parser-funding-applicants.json`

### After import:
- open the workflow
- identify the Gmail Trigger node
- identify the two HTTP Request nodes that send to the HubSpot workflow

---

## 6. Create Gmail Credential in n8n

### Steps:
1. In n8n, go to **Credentials**
2. Create new credential
3. Choose **Gmail OAuth2**
4. Connect the Gmail account you want monitored
5. Save with a clear name like:
   - `Gmail - Funding Intake`

### If Google Cloud setup is required:
1. Open Google Cloud Console
2. Create/select a project
3. Enable Gmail API
4. Configure OAuth consent screen
5. Create OAuth client credentials
6. Add the n8n redirect URL
7. paste Client ID + Client Secret into n8n
8. complete authorization

### Then:
- assign the Gmail credential to the Gmail Trigger node
- save the workflow

---

## 7. Replace Webhook URL in Gmail Parser

In the Gmail parser workflow, find both HTTP Request nodes:
- `Send to HubSpot Workflow`
- `Send to HubSpot Workflow (Already Replied)`

Replace the placeholder URL:
- `https://YOUR_N8N_HOST/webhook/funding-applicant-intake`

with the real webhook URL copied from the imported HubSpot workflow.

Save the workflow after updating both nodes.

---

## 8. Verify Credential Linkage

Before testing, confirm:

### HubSpot workflow
- every HubSpot HTTP node has a valid credential assigned

### Gmail parser workflow
- Gmail Trigger has a valid Gmail credential
- both HTTP nodes point to the real webhook URL

A ton of “workflow bugs” are really just broken credentials wearing fake moustaches.

---

## 9. Test the HubSpot Workflow First

Before turning on Gmail polling, test the HubSpot workflow directly.

Use one sample payload from:
- `assets/sample-webhook-payloads.json`

Recommended first test:
- Drid Guillaume
- Submission started

### Success criteria:
- contact search runs
- deal is created or updated
- deal description is populated correctly
- task sequence is created correctly
- webhook response returns success JSON

If this fails, fix it before enabling Gmail.

---

## 10. Test the Gmail Parser Workflow

Once the HubSpot workflow works:
- run/test the Gmail parser
- confirm it picks up a matching Gmail message
- confirm parsed output is reasonable
- confirm it posts to the HubSpot webhook successfully

### Verify:
- applicant email extracted correctly
- status extracted correctly
- route detected correctly
- `firstReplySent` branch behaves as expected

---

## 11. Validate Task Creation Behavior

Test both branches.

### Branch A — `firstReplySent = true`
Expected:
- Follow-up #2 (+2 days)
- Follow-up #3 (+4 days)
- Follow-up #4 (+7 days)
- Final follow-up (+12 days)

### Branch B — `firstReplySent = false`
Expected:
- Send first reply
- Follow-up #2 (Post-first-reply check-in)

---

## 12. Validate Stage Mapping

Confirm these status patterns behave correctly:

- Submission started → active early stage
- Application incomplete → waiting/action-needed stage
- Under review → in-review style stage
- Funded → `closedwon`
- Client file closed → `closedlost`

Important:
`Client file closed` should now map to `closedlost` based on the patched workflow.

---

## 13. Recommended First Live Run

Do not unleash this thing on the whole inbox immediately.

Start with:
- one known applicant thread
- one known test payload
- one confirmed HubSpot contact

Then scale.

This is operations, not speed dating.

---

## 14. Recommended Immediate Next Upgrades

After baseline setup works, add these next:

### Priority 1
- Gmail processed labels
- Gmail reply detection instead of inferred `firstReplySent`
- error handling / manual review branch

### Priority 2
- logging to Google Sheets / Notion / Airtable
- exception notifications to Slack or email
- route-based branching improvements

### Priority 3
- Wix applicant portal sync
- dedicated HubSpot Funding Applicants pipeline
- richer operator dashboards

---

## 15. Rebuild Checklist Summary

If rebuilding from scratch, do it in this order:

1. confirm repo files
2. import HubSpot workflow
3. create HubSpot credential
4. import Gmail parser workflow
5. create Gmail credential
6. replace webhook URL
7. test HubSpot workflow directly
8. test Gmail parser workflow
9. validate task behavior
10. validate status/stage mapping
11. enable workflows carefully

---

## Final Operator Rule

Do not debug 5 moving parts at once.

Always isolate:
1. payload
2. credentials
3. webhook
4. HubSpot logic
5. Gmail parsing

That is how you avoid losing an afternoon to nonsense.
