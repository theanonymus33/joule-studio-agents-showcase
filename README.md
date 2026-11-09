# Joule Studio Agents Showcase

Two projects built with **SAP Joule Studio**, both strictly **read-only and advisory**: a new conversational agent and an extension of an existing SAP agent.

> Built as part of my SAP Certified – Joule Studio Developer (C_JSD) preparation. Company and customer names (Barton Industrial Supply, Alpine Manufacturing, Stonebridge Equipment Group) are fictional scenario data, and all results come from generated mock data.

| Project | Type | What it does |
|---|---|---|
| [Customer Billing Review Agent](#1-customer-billing-review-agent) | New conversational agent (Python, A2A) | Compares a customer's payment behavior with contract terms and recommends what needs attention |
| [Financial Closing Escalation Extension](#2-financial-closing-escalation-extension) | Instruction-based extension of the Financial Closing Assistant | Flags critical period-end closing issues in priority order |

Both followed Joule Studio's intent-based flow: **Intent → Requirements (PRD) → Specification → Implementation**.

---

## 1. Customer Billing Review Agent

**Problem:** billing specialists had to check billing records and contract terms separately and compare them by hand (about 20 minutes per review, an illustrative baseline).

| Request type | Capability used | Result |
|---|---|---|
| Billing-only | Customer Billing Information | Balance, invoices, payment history |
| Contract-only | Contract Information | Payment terms, penalties, discounts, credit limit |
| Complete customer review | Both | Customer Billing Review Summary: discrepancies plus a justified recommendation |
| Write action (e.g. apply a credit) | None | Declined |
| Out of scope | None | Declined |

**Design decisions**
- Strictly read-only: no invoice or contract changes, credits or write-offs, payment arrangement changes, or collections actions.
- Scope-aware routing: classify the request first, then call only the capability it needs.
- Uses the two provided capabilities directly, with no custom integrations.
- Recommendations are advice for the specialist, never actions.
- Five business milestones are logged and traced (OpenTelemetry).

**Test results (mock data):** billing-only and contract-only requests each called one capability; the full review called both and produced a summary with 4 discrepancies; the write request was declined with no tool call. The build also passes its unit and integration test suite.

Documents: [intent](docs/billing-agent-intent.md)

---

## 2. Financial Closing Escalation Extension

**Problem:** finance teams at a multi-entity manufacturer spent about two hours finding critical issues in a period-end close with hundreds of interdependent tasks. **Goal:** identify and escalate them in 15 minutes.

| Rule | Condition | Classification |
|---|---|---|
| High-value posting error | Posting error with amount **exceeding** EUR 100,000 | Critical - Immediate Action Required |
| Close deadline proximity | Incomplete task with **fewer than four hours** to the period-end deadline | Urgent - Deadline Imminent |
| Overdue task escalation | Task past its scheduled completion and **blocking downstream dependencies** | Flag - Schedule Risk |

**Output:** a consolidated escalation summary ordered Critical → Urgent → Flag. Each entry has a task or document reference, the escalation reason, and recommended next steps. An item matching several rules shows all of its conditions, at its highest tier.

**Design decisions**
- Instruction-only: the base agent exposes a single Default capability instruction extension point, so the extension adds no hooks, tools, or integrations.
- Additive: every existing capability of the Financial Closing Assistant is preserved.
- Strictly advisory: it never posts documents, completes tasks, approves entries, or changes the schedule.
- Strict thresholds: exactly EUR 100,000 and exactly four hours do not trigger a rule.
- If data is missing or ambiguous, it says so and does not guess a classification.

Files: [extension.yaml](extensions/closing-escalation-agent-extension/extension.yaml) · [intent](docs/closing-extension-intent.md) · [PRD](docs/closing-extension-prd.md)

---

## Tech
SAP Joule Studio, SAP Financial Closing Assistant, Python A2A agent, MCP capabilities, OpenTelemetry.
