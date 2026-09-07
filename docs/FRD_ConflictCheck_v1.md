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
> A Matter record's Status field cannot be changed to "Active" or "Closed" unless its linked Conflict Check record exists and has a Result value of "Cleared."

*Plain-language version:* the system blocks the status change and returns an error message to the user if someone tries to activate or close a matter before the conflict check is cleared.

*Scope note (added 9/6/26):* extended beyond the original literal requirement (which only named Active) to also cover Closed, so a Matter can't be closed out while its conflict check remains unresolved either. The same underlying check — Result is not equal to Cleared — governs both transitions.

**Rule 2 — Permission gate on clearing:**
> Only users with the "Compliance" or "Partner" role may set a Conflict Check record's Result field to "Cleared."

*Plain-language version:* an Intake Coordinator can create the Client and Matter records and can even create a Conflict Check record, but they cannot mark it "Cleared" themselves — that action is restricted to Compliance or Partner-level users.

**Rule 3 — Flagged result handling:**
> If a Conflict Check's Result is set to "Flagged — Needs Review" or "Conflict Identified," the related Matter's Status remains locked at "Draft" and cannot be changed by any user until the Conflict Check record is updated to "Cleared."

*Implementation note (added 9/6/26):* enforced via Rule 1's Validation Rule — no separate rule needed. Since Matter's Status field has only three possible values (Draft / Active / Closed), "remains locked at Draft" and "cannot be changed by any user" is equivalent to "cannot become Active or Closed," which Rule 1 already blocks whenever Result is not Cleared. Documented here as a distinct rule because it describes a different stakeholder concern (the consequence of a flagged result) than Rule 1 (the forward-looking gate) — but both are satisfied by the single Validation Rule built for Rule 1.

## 4. Roles & Permissions Summary

| Role | Create Client/Matter | Create Conflict Check | Set Result = Cleared | Change Matter Status to Active |
|---|---|---|---|---|
| Intake Coordinator | Yes | Yes | No | No (blocked by Rule 1 unless already Cleared) |
| Compliance | Yes | Yes | Yes | Yes |
| Partner | Yes | Yes | Yes | Yes |
| Attorney (non-Partner) | View only | View only | No | No |

*Gap identified and closed (added 9/6/26):* the original table above never addressed Engagement Letter access at all. Added below, reasoned through by role:

| Role | Create | Read | Edit | Delete |
|---|---|---|---|---|
| Intake Coordinator | No | Yes | No | No |
| Compliance | No | Yes | No | No |
| Partner | Yes | Yes | Yes | No |
| Attorney (non-Partner) | Yes | Yes | Yes | No |

Reasoning: Intake Coordinator's job ends at intake (Client/Matter/Conflict Check setup) and never plausibly extends to drafting or managing an engagement letter, so View-only is appropriate here despite Intake Coordinator having Create access elsewhere. Compliance needs visibility for audit purposes (FR-5) but no operational need to create or edit engagement letters. Partner retains full access, consistent with the rest of the table. Attorney is a deliberate exception to their "View only" pattern everywhere else: as the person actually assigned to and managing a given matter (Responsible Attorney), an attorney needs to create, send, and update the engagement letter for their own matters — View-only would make it impossible for them to do work the role clearly requires.

*Delete permission, addressed for the first time across the entire system (added 9/6/26):* the original table never specified Delete for any role, on any object. Decision: Delete remains unchecked for all four roles above, across every object (Client, Matter, Conflict Check, Engagement Letter) — reserved exclusively for the System Administrator profile, which already exists in the org by default and requires no additional configuration. Rationale: none of the four business roles' actual responsibilities require permanently removing a record, and unrestricted delete access would undermine the audit trail FR-5 requires — Compliance being able to view conflict-check history on request has little value if records can also be deleted by the people being audited. Reserving Delete for a dedicated system-administration function, separate from all four legal-practice roles, follows the Principle of Least Privilege: no role holds a permission its actual job never requires.

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
