# Task Sequence

This document defines the applicant follow-up cadence used by the HubSpot funding applicant workflow.

The goal is to:

- get the applicant to complete the process
- reduce reply friction
- keep Moonshine Capital positioned as the helpful human layer
- provide a second engagement path
- open the door to alternative options without sounding pushy

---

## Core Strategy

The follow-up sequence assumes that many applicants:
- start an application
- get routed
- then stall
- or disappear into inbox purgatory

The system is designed to keep light pressure on the file without becoming annoying.

---

## Standard Cadence

### Email 1
- same day or next morning
- initial follow-up
- may already be sent manually

### Email 2
- 2 days later

### Email 3
- 4 days later

### Email 4
- 7 days later

### Email 5
- 10 to 14 days later

In the current workflow implementation, the final task is set at **+12 days**.

---

## Workflow Logic

### If first reply has already been sent
The workflow creates these tasks:

1. Follow-up #2 (+2 days)
2. Follow-up #3 (+4 days)
3. Follow-up #4 (+7 days)
4. Final follow-up / close loop (+12 days)

### If first reply has NOT been sent
The workflow creates:

1. Send first reply (immediate)
2. Follow-up #2 (+2 days)

---

## Task Naming Convention

Tasks use this format:

`[Applicant Name] — [Follow-Up Label]`

Examples:
- `Drid Guillaume — Follow-up #2 (Group + Resources)`
- `Drid Guillaume — Follow-up #3 (Alt options nudge)`
- `Drid Guillaume — Follow-up #4 (Simplify + friction removal)`
- `Drid Guillaume — Final follow-up / close loop`

---

## Task Sequence Details

### Follow-up #2
**Timing:** +2 days  
**Purpose:** Group invite + resources + community angle

**Task body:**
Send Email 2: invite to Funding Applicants Hub + resources + secondary engagement path.

---

### Follow-up #3
**Timing:** +4 days  
**Purpose:** Soft nudge + offer alternative funding paths

**Task body:**
Send Email 3: soft nudge + offer alternative funding paths.

---

### Follow-up #4
**Timing:** +7 days  
**Purpose:** Simplify process + remove friction

**Task body:**
Send Email 4: simplify process + reduce friction + quick reply prompt.

---

### Final follow-up
**Timing:** +12 days  
**Purpose:** Close the loop without sounding robotic or desperate

**Task body:**
Send Email 5: final bump + open loop + soft exit.

---

## Example Email Themes

### Email 1 — Heads up + easiest reply path
Purpose:
- explain who Jason is
- explain why he is replying
- reduce mystery / weird internet finance energy
- give simple reply options
- keep alternative options open

### Email 2 — Group invite + resources
Purpose:
- move applicant into a private space
- reduce isolation/confusion
- position Moonshine Capital as a helpful operating layer

### Email 3 — Soft nudge + alternative options
Purpose:
- give a low-friction way to ask for help
- offer route alternatives without pressure

### Email 4 — Simplify and remove friction
Purpose:
- acknowledge the process gets annoying
- reduce overwhelm
- ask for a one-sentence reply

### Email 5 — Final bump / open loop
Purpose:
- politely end the chase
- leave the door open
- keep goodwill

---

## HubSpot Task Association Rules

Each generated task should be associated with:
- the applicant Deal
- the applicant Contact

This ensures:
- task visibility on the deal timeline
- task visibility on the contact timeline
- cleaner internal follow-up operations

---

## Task Creation Rules by Applicant State

### Submission started
If first reply already sent:
- create follow-up #2–#5 sequence

If first reply not sent:
- create first reply task
- create follow-up #2

### Application incomplete
Use the same active follow-up sequence, but consider priority = high.

### Under review
Use fewer nurture-style tasks if the provider is actively reviewing.

### Funded
Do not create chase tasks.
Instead, consider future improvement:
- create “send congrats / offer next resources” task

### Dead / Closed / Withdrawn
Do not create chase tasks.
Optionally create:
- internal review or archive task

---

## Recommended Future Enhancements

### Add reply detection
Instead of guessing whether the first reply was sent, inspect the Gmail thread.

### Add label-based cadence control
Use Gmail labels such as:
- `DAC/Needs Follow-Up`
- `DAC/Already Replied`
- `DAC/Funded`

### Add status-aware branching
Make the workflow smarter by changing cadence based on:
- route
- applicant responsiveness
- provider review status
- funding result

---

## Summary

This task sequence is not just about “sending follow-ups.”

It is designed to turn stalled applicant chaos into a controlled system:
- clear cadence
- clear next steps
- low-friction support
- reusable automation logic
