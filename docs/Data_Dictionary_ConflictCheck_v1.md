# Data Dictionary — Conflict Check Enforcement

This document explains what each piece of tracked information means and why it's collected, written for anyone at the firm — not just technical staff. For the underlying technical field types and system rules, see the [FRD](./FRD_ConflictCheck_v1.md).

## Client

Information about the person or entity the firm may represent.

| Field | What it means |
|---|---|
| Client Name | The name of the person or organization seeking representation |
| Contact Info | How to reach the client — phone or email |
| Related Parties | Anyone connected to this client who could create a conflict of interest with another matter — for example, a business partner, spouse, or affiliated company. This is the information Compliance reviews when checking for conflicts |

## Matter

A specific case or engagement the firm is handling for a client.

| Field | What it means |
|---|---|
| Matter Name | A short name identifying this specific case or engagement |
| Client | Which client this matter belongs to |
| Practice Area | The type of legal work involved (e.g., Litigation, Family, Estate Planning) |
| Responsible Attorney | The attorney assigned to handle this matter |
| Status | Where this matter stands: Draft (not yet cleared to begin), Active (cleared and underway), or Closed |
| Conflict Check | The conflict review linked to this matter — required and must be cleared before the matter can become Active |

## Conflict Check

The formal review confirming a new matter doesn't conflict with an existing client relationship.

| Field | What it means |
|---|---|
| Related Matter | Which matter this review was performed for |
| Date Checked | When the review was performed |
| Checked By | Who performed the review |
| Result | The outcome: Cleared (no conflict found, matter may proceed), Flagged — Needs Review (a possible conflict was found and needs a closer look), or Conflict Identified (a real conflict exists) |
| Notes | Additional explanation, especially important if the result is Flagged or a conflict was identified |

## Engagement Letter

The formal agreement confirming a client and the firm have agreed to the terms of representation.

| Field | What it means |
|---|---|
| Related Matter | Which matter this agreement covers |
| Status | Whether the letter has been sent to the client and/or signed |
| Client Signed Date | The date the client signed; a filled-in date means the client has signed |
| Firm Signed Date | The date the firm/attorney countersigned |
| Engagement Letter Document | The actual signed document itself, stored and attached to the record |

## A Note on Terms Used

- **Related/Linked record** — one piece of tracked information pointing to another (e.g., a Matter pointing to its Client)
- **Result/Status field** — a fixed set of choices someone selects from, rather than free-form text, so answers stay consistent and reportable
