# Prompts

This file stores reusable prompts for building, debugging, extending, and maintaining the workflows in `funding-applicant-ops`.

Use these prompts to move faster without reinventing the wheel every time.

---

## Prompt 1 — Audit Workflow JSON Against Docs

Use this when you want to review workflow drift.

```text
Audit the workflow JSON files in this repo and compare them against the docs.

Check for mismatches in:
- payload shape
- field mapping
- stage mapping
- task cadence
- dedupe logic
- known limitations documented vs actual logic

Return:
1. aligned items
2. mismatches
3. bugs
4. recommended fixes
5. a patch plan ranked by priority
```

---

## Prompt 2 — Generate Sample Payloads

Use this when you need more realistic test data.

```text
Create realistic sample applicant webhook payloads for this repo.

Include examples for:
- submission started
- application incomplete
- under review
- funded
- client file closed
- ambiguous route
- missing applicant email

Return valid JSON only.
```

---

## Prompt 3 — Improve Gmail Parsing

Use this when the parser starts missing real-world email patterns.

```text
Review the Gmail parser logic in this repo and improve the regex / parsing rules.

Goals:
- increase extraction accuracy for applicant email, name, phone, business name, route, revenue, and bank type
- reduce false positives
- preserve existing payload structure

Return:
1. parsing weaknesses found
2. revised code node logic
3. edge cases handled
4. notes on any risky assumptions
```

---

## Prompt 4 — Improve Dedupe Logic

Use this when duplicate HubSpot deals start showing up.

```text
Review the HubSpot deal dedupe logic in this repo.

Current intent:
- match contacts by exact email
- match deals by applicant name in deal name
- match deals by applicant email in description

Improve the logic to reduce duplicate deal creation while avoiding false matches.

Return:
1. weaknesses in current dedupe logic
2. improved dedupe strategy
3. revised code snippet
4. any data risks introduced by stricter or looser matching
```

---

## Prompt 5 — Add Reply Detection

Use this when upgrading beyond inferred `firstReplySent`.

```text
Design a v2 reply-detection upgrade for this repo.

Goal:
replace inferred `firstReplySent` logic with actual Gmail thread inspection.

Return:
1. proposed workflow changes
2. Gmail thread inspection logic
3. updated payload contract
4. fallback behavior when thread evidence is unclear
5. exact code changes where possible
```

---

## Prompt 6 — Generate Operator Docs

Use this when adding or refreshing repo docs.

```text
Create operator-facing markdown docs for this repo.

Possible files:
- agents.md
- prompts.md
- setup-checklist.md
- troubleshooting.md
- workflow-maintenance.md

Write them to match the repo's current style:
- clear
- practical
- internal-ops focused
- minimal fluff
```

---

## Prompt 7 — Create a Patch Plan

Use this before changing workflow files.

```text
Review this repo and turn findings into a patch plan.

Rules:
- prioritize real bugs first
- separate critical fixes from nice-to-haves
- avoid unnecessary refactors
- preserve payload compatibility unless there is a strong reason to change it

Return:
1. critical fixes
2. medium-priority improvements
3. optional cleanups
4. exact files affected
5. recommended commit order
```

---

## Prompt 8 — Generate n8n Import JSON

Use this when creating a new workflow or revised version.

```text
Generate n8n-ready import JSON for a workflow that integrates with the existing repo structure.

Requirements:
- preserve repo naming conventions
- preserve existing payload shape unless explicitly changed
- include realistic node names
- include comments/logic explanation outside the JSON if needed
- ensure JSON is valid for import
```

---

## Prompt 9 — Troubleshoot a Broken Run

Use this when n8n or HubSpot starts acting possessed.

```text
Troubleshoot this workflow failure.

Context:
- repo: funding-applicant-ops
- workflow: [name]
- failed node: [node name]
- error: [paste exact error]
- expected behavior: [describe]

Return:
1. likely root cause
2. exact fix
3. whether this is a code issue, credential issue, payload issue, or API issue
4. any follow-on risks
```

---

## Prompt 10 — Expand the System

Use this when adding future lanes.

```text
Design the next expansion layer for this repo.

Possible expansions:
- Gmail labels and processed-state logic
- Google Sheets logging
- Notion logging
- Slack alerts
- Wix applicant hub sync
- alternative route branching
- manual review queue

Return:
1. best next expansion
2. why it matters
3. how it fits the current repo
4. files/workflows to add or modify
```

---

## Operator Note

These prompts are meant to reduce wasted motion.

The repo should behave like an operating system, not a junk drawer.
