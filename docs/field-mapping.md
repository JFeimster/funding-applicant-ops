# Field Mapping

This document defines the field mapping between:

1. parsed Gmail applicant payload
2. HubSpot contact/deal fields
3. derived workflow logic
4. task creation behavior

---

## Source Payload Fields

These are the normalized fields expected by the HubSpot intake workflow.

| Payload Field | Type | Description |
|---|---|---|
| `source` | string | Source system or sender context |
| `emailSubject` | string | Original Gmail subject line |
| `applicantName` | string | Applicant full name |
| `applicantEmail` | string | Applicant email address |
| `phone` | string | Applicant phone number |
| `businessName` | string | Business/company name if present |
| `routeDetected` | string | Route inferred from email content |
| `revenue` | string | Monthly revenue if parsed |
| `bankType` | string | Personal or Business |
| `status` | string | Workflow status inferred from subject/body |
| `firstReplySent` | boolean | Whether first manual follow-up has already been sent |
| `messageId` | string | Gmail message ID |
| `threadId` | string | Gmail thread ID |
| `notes` | string | Extra parser/workflow notes |

---

## Contact Matching

### Primary contact lookup
HubSpot contact matching is performed using:

| Payload Field | HubSpot Object | HubSpot Property |
|---|---|---|
| `applicantEmail` | Contact | `email` |

### Current behavior
- search HubSpot contacts by exact email
- if a contact exists, use it
- if no contact exists, current workflow does not auto-create a contact unless added later

---

## Deal Field Mapping

### Direct mapping

| Payload Field / Derived Value | HubSpot Deal Property | Notes |
|---|---|---|
| derived `dealName` | `dealname` | `[Applicant Name] | DAC Applicant | [RouteShort]` |
| derived `pipeline` | `pipeline` | currently `default` |
| derived `dealStage` | `dealstage` | mapped from applicant status |
| derived `dealType` | `dealtype` | currently `newbusiness` |
| derived `priority` | `hs_priority` | high / medium / low |
| derived `description` | `description` | structured multiline applicant summary |
| derived `nextStep` | `hs_next_step` | controlled follow-up phrase |
| derived `hubspotOwnerId` | `hubspot_owner_id` | currently Jason owner id |

---

## Deal Name Logic

### Format
`[Applicant Name] | DAC Applicant | [RouteShort]`

### Route shortening
| `routeDetected` | `RouteShort` |
|---|---|
| `Giggle Finance` | `Giggle` |
| `BankBreezy` | `BankBreezy` |
| unknown / other | `Unknown` |

### Example
`Drid Guillaume | DAC Applicant | Giggle`

---

## Description Mapping

The workflow builds a structured multiline description.

### Format
```text
Source:
Original email subject:
Applicant email:
Owner phone:
Business name:
Route detected:
Revenue:
Bank type:
Current blocker:
Last meaningful update:
Notes:
Gmail Message ID:
Gmail Thread ID:
