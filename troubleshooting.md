# Troubleshooting

Use this file to diagnose common failures in the `funding-applicant-ops` system.

The point is not to sound smart.
The point is to get the machine working again without wasting half a day chasing ghosts.

---

## Debug Order

When something breaks, isolate the problem in this order:

1. payload
2. credentials
3. webhook
4. HubSpot logic
5. Gmail parsing
6. downstream task creation

Do not debug six layers at once unless you enjoy self-inflicted chaos.

---

## Symptom: Gmail parser runs, but nothing reaches HubSpot

### Likely causes
- webhook URL in Gmail parser is still the placeholder
- webhook URL is wrong
- HubSpot workflow is inactive or not saved
- HTTP Request node is pointed at the wrong environment

### What to check
- open `gmail-parser-funding-applicants.json` in n8n
- inspect both nodes:
  - `Send to HubSpot Workflow`
  - `Send to HubSpot Workflow (Already Replied)`
- confirm the URL is the real webhook URL from the imported HubSpot workflow
- confirm the HubSpot workflow is saved and active if required for your setup

### Fix
Replace the placeholder webhook URL and retest with a known payload.

---

## Symptom: HubSpot workflow runs, but contact is not found

### Likely causes
- applicant email was parsed incorrectly
- applicant email is missing from payload
- the contact does not exist in HubSpot
- email formatting in the source message is weird or malformed

### What to check
- inspect the incoming payload
- confirm `applicantEmail` is present and correct
- search HubSpot manually by that email

### Fix
- improve parser regex
- create contact separately if your system requires it
- add manual review handling for payloads with missing email

---

## Symptom: Duplicate deals are being created

### Likely causes
- applicant email not parsed consistently across emails
- deal name matching is too weak
- description search misses slightly different email/state variants
- route changes cause new deal names while existing deal still exists

### What to check
- compare the applicant payload across multiple emails for the same person
- inspect HubSpot deal search results
- confirm whether the existing deal description contains the applicant email

### Fix
- strengthen dedupe logic
- normalize payload fields harder before search
- consider storing a stronger unique identifier later if needed

---

## Symptom: `Client file closed` is not landing in lost/closed stage

### Likely causes
- workflow file was not updated after the patch
- n8n is still using an older imported version
- the status string differs from expected text

### What to check
- inspect the current HubSpot workflow code node
- confirm dead-state regex includes:
  - `closed`
  - `client file closed`

### Fix
Re-import or update the HubSpot workflow to the patched version.

---

## Symptom: Tasks fail to create in HubSpot

### Likely causes
- tasks are being created without required associations
- contact ID is null
- deal ID is null
- HubSpot credential is missing required permissions

### What to check
- inspect task creation node payload
- confirm `finalDealId` exists
- confirm `contactId` exists
- confirm task payload includes associations

### Fix
- associate tasks to the deal and the contact
- ensure the contact search succeeded
- verify HubSpot auth scopes / private app permissions

---

## Symptom: Tasks are created, but the sequence is wrong

### Likely causes
- `firstReplySent` value is wrong
- Gmail parser inferred active follow-up incorrectly
- test payload does not match the intended branch

### What to check
- inspect payload entering HubSpot workflow
- confirm `firstReplySent` is true or false as expected
- compare the result against `docs/task-sequence.md`

### Fix
- correct the test payload
- improve parser logic
- eventually replace inferred logic with actual Gmail thread reply detection

---

## Symptom: Applicant status maps to the wrong deal stage

### Likely causes
- regex mapping is too broad or too narrow
- status text differs slightly from expected patterns
- Sales Pipeline fallback mapping is still a hacky compromise

### What to check
- inspect `status` in payload
- inspect `dealStage` derivation in the code node
- compare against `docs/field-mapping.md`

### Fix
- patch regex mapping
- add additional known status patterns
- later move to a dedicated Funding Applicants pipeline

---

## Symptom: Gmail parser misses applicant name, phone, or business name

### Likely causes
- source email format changed
- regex patterns are too narrow
- message content is mostly HTML or oddly formatted

### What to check
- inspect raw Gmail subject/snippet/body used by the parser
- compare failed email against successful email examples
- identify what labels/phrases actually appear in the message

### Fix
- expand parser regex patterns
- normalize HTML/text extraction better
- keep a library of weird real-world examples

---

## Symptom: Gmail parser marks `firstReplySent = true` when it shouldn’t

### Why this happens
This is a known v1 limitation.
The current parser infers `firstReplySent` from status patterns like:
- Submission started
- Application incomplete
- Under review

That is useful, but not authoritative.

### Fix
Future upgrade:
- inspect the Gmail thread
- verify whether Jason actually sent a reply
- use evidence instead of assumption

### Current workaround
For sensitive cases:
- manually inspect thread
- test payload directly into HubSpot workflow with corrected `firstReplySent` value

---

## Symptom: Credential errors or auth failures

### Likely causes
- Gmail OAuth expired
- HubSpot credential missing/invalid
- private app token rotated
- imported workflow still references placeholder credential IDs

### What to check
- open each node that uses a credential
- confirm a real saved credential is selected
- test the credential in n8n

### Fix
- reconnect Gmail OAuth
- regenerate HubSpot private app token if needed
- reassign credentials in all affected nodes

---

## Symptom: Workflow imports successfully, but nodes are still broken

### Likely causes
- imported JSON contains placeholder credential IDs
- n8n does not auto-map credentials
- placeholder webhook URLs were not replaced

### Fix
After import, always manually:
- assign Gmail credential
- assign HubSpot credential
- replace placeholder webhook URLs
- save workflow again

---

## Symptom: Successful run, but bad data in HubSpot description

### Likely causes
- parser extracted the wrong email/name
- route detected incorrectly
- source email body includes misleading text

### What to check
- inspect normalized payload
- compare description field against original email
- verify regex patterns for name/email extraction

### Fix
- tighten regex
- add route-specific parsing logic later
- improve normalization before writing to HubSpot

---

## Symptom: Workflow works in test, but not on live polling

### Likely causes
- Gmail query is too broad or too narrow
- Gmail trigger is picking up unexpected message formats
- live inbox contains more variation than test cases

### What to check
- inspect actual Gmail messages being picked up
- compare with test payloads in `assets/sample-webhook-payloads.json`
- review skipped or malformed cases

### Fix
- refine Gmail query
- add labels
- add processed-state logic
- add manual review branch for edge cases

---

## Fast Triage Questions

When something breaks, ask these first:

1. Did the payload reach the webhook?
2. Was applicant email parsed correctly?
3. Did contact search return a real contact?
4. Did deal search return an existing match?
5. Was a deal created or updated?
6. Did task creation receive valid deal/contact IDs?
7. Did status map to the expected stage?

If you can answer those, you usually find the problem fast.

---

## Recommended Debug Tools

Useful assets already in this repo:
- `docs/field-mapping.md`
- `docs/task-sequence.md`
- `assets/sample-webhook-payloads.json`
- `prompts.md`

Best practice:
- test the HubSpot workflow directly first
- then test Gmail parser second
- only then enable full live polling

---

## Final Rule

Do not confuse:
- a parsing problem
- a credential problem
- a webhook problem
- a HubSpot logic problem

They look similar from a distance.
They are not the same problem.

Separate them, and the fix usually reveals itself.
