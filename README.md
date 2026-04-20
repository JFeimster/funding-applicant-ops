# funding-applicant-ops

n8n workflows and documentation for parsing Gmail applicant emails, creating/updating HubSpot deals, generating follow-up tasks, and systematizing funding applicant operations for Moonshine Capital.

---

## Overview

This repo contains the core workflow assets for a lightweight **Gmail → HubSpot applicant operations system**.

The system is designed to:

- monitor Gmail for applicant/status emails
- parse applicant details from inbound messages
- create or update HubSpot deal records
- associate deals with matching contacts
- create follow-up tasks automatically
- reduce duplicate records
- standardize applicant follow-up operations

This repo is especially useful for handling funding applicants routed through partners such as **Giggle Finance** and **BankBreezy**, while keeping Moonshine Capital as the human support layer.

---

## Repo Structure

```text
funding-applicant-ops/
├── workflows/
│   ├── hubspot-funding-applicant-workflow.json
│   └── gmail-parser-funding-applicants.json
├── docs/
│   ├── field-mapping.md
│   └── task-sequence.md
├── assets/
│   └── sample-webhook-payloads.json
└── README.md
