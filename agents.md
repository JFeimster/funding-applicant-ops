# Agents

This file documents the core operator-facing agents and workflow roles inside `funding-applicant-ops`.

The goal is simple:
- make it obvious what each workflow does
- clarify inputs and outputs
- reduce operator confusion
- make future upgrades easier

---

## Agent 1 — Gmail Parser

**File:** `workflows/gmail-parser-funding-applicants.json`

### Purpose
Monitors Gmail for likely applicant/status emails, extracts structured applicant data, and sends normalized payloads into the HubSpot workflow.

### Core responsibilities
- watch Gmail for relevant applicant/status emails
- parse applicant email, name, phone, business name, route, revenue, bank type, and status
- infer whether the applicant is likely already in active follow-up
- forward normalized payload to the HubSpot intake workflow

### Trigger conditions
Examples:
- Submission Started
- Application Incomplete
- Client File Closed
- Funded
- Approved
- Under Review
- Giggle
- BankBreezy

### Inputs
Raw Gmail message data.

### Outputs
Normalized applicant payload posted to the HubSpot webhook workflow.

### Known limitation
`firstReplySent` is inferred in v1 based on status, not verified from actual sent-reply detection in the Gmail thread.

---

## Agent 2 — HubSpot Intake Writer

**File:** `workflows/hubspot-funding-applicant-workflow.json`

### Purpose
Receives normalized applicant payloads, finds the right HubSpot contact/deal, applies dedupe logic, creates or updates the deal, and creates follow-up tasks.

### Core responsibilities
- accept webhook payload from parser workflow
- search HubSpot contacts by applicant email
- search HubSpot deals by applicant name / email in description
- avoid duplicate deal creation
- create/update the deal with standardized fields
- create task sequence based on applicant state

### Inputs
Normalized applicant payload from parser workflow.

### Outputs
- created or updated HubSpot deal
- associated follow-up tasks
- webhook response payload with summary

### Important behavior
- uses default pipeline mapping for now
- maps dead / closed / withdrawn / lost style statuses to `closedlost`
- creates richer follow-up sequence if `firstReplySent = true`

---

## Agent 3 — Human Operator

### Purpose
Reviews exceptions, handles messy real-world edge cases, and decides when a file needs intervention beyond automation.

### Core responsibilities
- review parsing misses
- review ambiguous matches
- manually inspect Gmail threads when needed
- revise follow-up copy if a case is unusual
- confirm whether applicant should be nurtured, rerouted, or marked dead

### Typical intervention moments
- no applicant email extracted
- multiple possible HubSpot matches
- route detected is unclear
- the workflow created a deal but the status feels wrong
- applicant replied in a way the automation cannot interpret cleanly

---

## Future Agents Worth Adding

### Reply Detection Agent
Purpose:
- inspect Gmail thread to verify whether Jason actually replied
- replace inferred `firstReplySent` logic with real evidence

### Error Review Agent
Purpose:
- catch failed or ambiguous workflow runs
- send exception alerts to Slack / email / Notion

### Logging Agent
Purpose:
- log every processed applicant to Sheets, Notion, or Airtable
- create an audit trail

### Wix Sync Agent
Purpose:
- sync applicants into a Wix-facing applicant hub or portal later

---

## Suggested Mental Model

Think of this repo as a small operations team:

- **Gmail Parser** = intake scout
- **HubSpot Intake Writer** = CRM operator
- **Human Operator** = closer / exception handler

That framing makes expansion easier later.
