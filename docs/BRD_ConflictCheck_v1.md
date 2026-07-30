# Business Requirements Document (BRD)
## Conflict-of-Interest Check Enforcement — Client Intake Process

**Project:** Legal Practice Management Platform — Matter Intake Module
**Author:** Monica Johnson
**Date:** July 28, 2026
**Status:** Draft
**Document type:** BRD (business case) — see companion FRD for technical/functional detail

---

## 1. Business Situation

A legal practice management SaaS platform serves small-to-mid-size law firms, giving them a system of record for clients, matters, billing, and case documentation. Firms use this platform to open new client matters and track them from intake through closure.

## 2. Problem Statement

Conflict-of-interest checks are a required ethical and legal step before a firm can take on a new matter — the firm must confirm no existing client relationship creates a conflict with the new one. Today, this check is performed manually by intake staff, and its completion depends entirely on individual diligence. There is no system-enforced requirement that a conflict check happen, or happen correctly, before a matter proceeds.

This creates real exposure: a matter can currently become active with no conflict check on record at all. For a law firm, this isn't just a process inefficiency — it's a genuine malpractice and bar-compliance risk.

## 3. Business Objective

Ensure that no new client matter can become active in the system until a conflict-of-interest check has been performed, documented, and formally cleared by an authorized reviewer — removing reliance on individual staff memory or diligence.

## 4. Scope

**In scope:**
- Enforcing a conflict-check requirement before a Matter can move from Draft to Active status
- Capturing who performed the check, when, and the result
- Restricting who is authorized to mark a conflict check as "Cleared"

**Out of scope (for this phase):**
- Automating the conflict search itself (i.e., actually cross-referencing client/related-party names against existing records) — this phase enforces that a human-reviewed check occurred and was documented, not that the search process itself is automated
- Billing integration, or automated document-generation/e-signature workflows for engagement letters — this phase covers manual tracking of signing status and secure document storage, not automated generation or e-signature platform integration *(scope revised July 29, 2026 — see FRD for expanded Engagement Letter data model)*

## 5. Stakeholders

| Stakeholder | Interest |
|---|---|
| Managing Partner | Ultimate accountability for firm ethics compliance; needs assurance no matter proceeds unchecked |
| Compliance/Ethics Officer | Responsible for reviewing and clearing conflict checks; needs a clear, auditable record |
| Intake Coordinator | Opens new client/matter records day-to-day; needs a clear, unambiguous process that doesn't block their normal workflow unnecessarily |
| Responsible Attorney (per matter) | Needs confidence the matter they're assigned to has been properly cleared before doing substantive work |

## 6. Success Criteria

- Zero matters reach Active status without a documented, cleared conflict check on record
- Compliance can produce an audit trail (who checked, when, result) for any matter on request
- Intake staff can complete the required steps without needing to remember the rule manually — the system itself blocks the wrong sequence

## 7. Assumptions & Constraints

- This is a portfolio/demonstration project using a synthetic scenario and no real client data
- The underlying platform is assumed to support custom objects, fields, and status-based workflow automation (Salesforce-style CRM architecture), consistent with the target company profile for this project
- Role-based permissions are assumed to be configurable at the platform level

## 8. Next Step

See the companion **Functional Requirements Document (FRD)** for the specific data model, field definitions, and business-rule/trigger logic that implements this requirement.
