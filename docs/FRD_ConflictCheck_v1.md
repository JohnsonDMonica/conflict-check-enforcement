# Functional Requirements Document (FRD)
## Conflict-of-Interest Check Enforcement — Client Intake Process

**Project:** Legal Practice Management Platform — Matter Intake Module
**Author:** Monica Johnson
**Date:** July 28, 2026
**Status:** Draft
**Companion document:** See BRD for business justification and objectives

---

## 1. Overview

This document translates the business requirement in the BRD — no matter proceeds without a cleared conflict check — into specific data structures, field definitions, business rules, and access controls.

## 2. Data Model (Objects & Fields)

### Object: Client
| Field | Type | Required? | Notes |
|---|---|---|---|
| Client Name | Text | Yes | |
| Contact Info | Text | Yes | Phone/email |
| Related Parties | Text (long) | No | Names of individuals/entities connected to this client — used as input for conflict review |

### Object: Matter
| Field | Type | Required? | Notes |
|---|---|---|---|
| Matter Name | Text | Yes | |
| Client | Lookup → Client | Yes | Links matter to its client record |
| Practice Area | Picklist | Yes | e.g., Litigation, Family, Estate Planning |
| Responsible Attorney | Lookup → User | Yes | |
| Status | Picklist | Yes | Draft / Active / Closed |
| Conflict Check | Lookup → Conflict Check | No (until Active) | Must be populated and Cleared before Status can become Active |

### Object: Conflict Check
| Field | Type | Required? | Notes |
|---|---|---|---|
| Related Matter | Lookup → Matter | Yes | |
| Date Checked | Date | Yes | |
| Checked By | Lookup → User | Yes | |
| Result | Picklist | Yes | Cleared / Flagged — Needs Review / Conflict Identified |
| Notes | Text (long) | No | Free-text explanation, especially if Flagged or Conflict Identified |

### Object: Engagement Letter
*(Scope revised July 29, 2026 — originally reference-only; fields below reasoned through directly)*
| Field | Type | Required? | Notes |
|---|---|---|---|
| Related Matter | Lookup → Matter | Yes | |
| Status | Picklist | Yes | Not Sent / Sent / Signed |
| Client Signed Date | Date | No | Populated once the client signs; presence of a date implies signed, same pattern as Conflict Check's Date Checked field |
| Firm Signed Date | Date | No | Populated once the firm/attorney countersigns |
| Engagement Letter Document | Attachment/File | No | The actual signed document itself; stored as a Salesforce File attached to the record, not a text field |

## 3. Business Rule / Trigger Logic

**Rule 1 — Status gate:**
> A Matter record's Status field cannot be changed to "Active" unless its linked Conflict Check record exists and has a Result value of "Cleared."

*Plain-language version:* the system blocks the status change and returns an error message to the user if someone tries to activate a matter before the conflict check is cleared.

**Rule 2 — Permission gate on clearing:**
> Only users with the "Compliance" or "Partner" role may set a Conflict Check record's Result field to "Cleared."

*Plain-language version:* an Intake Coordinator can create the Client and Matter records and can even create a Conflict Check record, but they cannot mark it "Cleared" themselves — that action is restricted to Compliance or Partner-level users.

**Rule 3 — Flagged result handling:**
> If a Conflict Check's Result is set to "Flagged — Needs Review" or "Conflict Identified," the related Matter's Status remains locked at "Draft" and cannot be changed by any user until the Conflict Check record is updated to "Cleared."

## 4. Roles & Permissions Summary

| Role | Create Client/Matter | Create Conflict Check | Set Result = Cleared | Change Matter Status to Active |
|---|---|---|---|---|
| Intake Coordinator | Yes | Yes | No | No (blocked by Rule 1 unless already Cleared) |
| Compliance | Yes | Yes | Yes | Yes |
| Partner | Yes | Yes | Yes | Yes |
| Attorney (non-Partner) | View only | View only | No | No |

## 5. Functional Requirements List (User Story Format)

- **FR-1:** As an Intake Coordinator, I need to create a new Client and Matter record so that a new engagement can begin being tracked.
- **FR-2:** As an Intake Coordinator, I need to initiate a Conflict Check record linked to the Matter so that the review process has a documented starting point.
- **FR-3:** As a Compliance Officer, I need to review related-party information and set the Conflict Check Result so that I can formally clear or flag the matter.
- **FR-4:** As the system, I need to prevent a Matter's Status from becoming Active unless its Conflict Check Result is Cleared, so that no matter proceeds without proper review.
- **FR-5:** As a Managing Partner, I need to be able to view the Conflict Check history for any Matter so that I can confirm compliance on request (audit trail).

## 6. Out of Scope (carried from BRD)

- Automated conflict-name matching/search logic
- Automated engagement letter generation/sending workflow (e.g., auto-emailing the document, e-signature platform integration) — this phase captures the data structure and manual signing/documentation status only
- Billing/invoicing integration
