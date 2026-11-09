# Customer Billing Review Agent (SAP Joule Studio)

A read-only conversational AI agent built with **SAP Joule Studio** that helps billing specialists check whether a customer's payment behavior matches the agreed contract terms.

> Built as part of my SAP Certified – Joule Studio Developer (C_JSD) preparation. The company and customer names (Barton Industrial Supply, Alpine Manufacturing) are fictional scenario data, and all results below come from generated mock data.

## The problem
Billing specialists had to look at billing records and contract terms separately, then compare them by hand (~20 minutes per customer review, illustrative baseline). This agent brings both sources into one conversation.

## What the agent does
| Request type | Capability used | Result |
|---|---|---|
| Billing-only | Customer Billing Information | Balance, invoice table, payment history |
| Contract-only | Contract Information | Payment terms, penalties, discounts, credit limit |
| Complete customer review | Both | Customer Billing Review Summary: discrepancies plus a justified recommendation |
| Write action (e.g. apply a credit) | None | Clearly declined |
| Out of scope | None | Clearly declined |

## Design decisions
- **Strictly read-only.** No tool in the agent can modify invoices or contracts, apply credits or write-offs, change payment arrangements, or start collections. Enforced in the system prompt and in code.
- **Scope-aware routing.** The agent classifies each request first and calls only the capability it needs.
- **Provided capabilities only.** Integrates Customer Billing Information and Contract Information directly, with no custom integrations.
- **Advisory output.** Recommendations are suggestions for the specialist, never actions.
- **Observability.** Five business milestones (classification, retrieval, discrepancy identification, summary, decline) are logged and traced.

## How it was built
Joule Studio's intent-based flow: Intent → Requirements (PRD) → Specification → Implementation. The intent document is in [`docs/intent.md`](docs/intent.md).

## Test results (mock data)
| Request | Capability called | Outcome |
|---|---|---|
| Billing situation for Alpine Manufacturing | Billing only | Balance, overdue invoices, late-payment history |
| Agreed payment terms for Alpine Manufacturing | Contract only | Net 30 terms, late penalty, early-payment discount, credit limit |
| Full review: compare payment behavior to contract | Both | Summary with 4 discrepancies and a justified recommendation |
| Apply a credit to the latest invoice | None | Declined as read-only |

The build also passes its unit and integration test suite (114 tests).

## Tech
SAP Joule Studio, Python A2A agent, MCP capabilities, OpenTelemetry.
