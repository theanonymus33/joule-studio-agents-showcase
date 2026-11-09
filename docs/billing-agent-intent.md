# Customer Billing Review Agent

Barton Industrial Supply — Conversational read-only AI agent for billing specialists

## Business challenge

Barton Industrial Supply sells equipment and spare parts to business customers. Billing specialists must check billing information and contract information separately to determine whether a customer's current payment behavior matches the agreed payment terms. A read-only conversational agent is needed that brings these two sources together, highlights relevant differences, and helps the billing specialist identify what requires attention.

The agent retrieves current billing data using the **Customer Billing Information** capability and contractual payment terms using the **Contract Information** capability. For billing-only requests it uses only Customer Billing Information; for contract-only requests it uses only Contract Information; for a complete customer review it uses both, identifies relevant differences, and produces a **Customer Billing Review Summary** with a justified recommendation. The agent must not modify invoices or contracts, apply credits or write-offs, change payment arrangements, or initiate collections actions. All out-of-scope requests must be clearly declined.

## Business Goals & Success Criteria

| Metric | Baseline | Target | Timeline | Process / Capability | Source |
|--------|----------|--------|----------|----------------------|--------|
| Time to complete a customer billing review | ~20 minutes | ~5 minutes | — | Invoice to Cash – Accounts Receivable Review | user |
| Correct scope selection (billing-only / contract-only / combined) | Manual, error-prone | 100% accurate scope handling | — | Customer Billing Review | user |
| Write-action prevention | Manual discipline | Zero write actions performed by agent | — | Data integrity / compliance | user |
| Out-of-scope request handling | No automated decline | All out-of-scope requests declined | — | Agent guardrails | user |

## Key Milestones

| Milestone | Condition |
|-----------|-----------|
| Request classified | Agent correctly identifies request type (billing-only, contract-only, or combined) and selects the appropriate capability |
| Data retrieved | Relevant data retrieved from Customer Billing Information and/or Contract Information as required by the request |
| Discrepancies identified | For combined reviews, differences between payment behavior and contract terms are flagged |
| Customer Billing Review Summary produced | A complete summary with justified recommendation is delivered to the billing specialist |
| Out-of-scope request declined | Any request outside billing and contract review is clearly declined without any data access |

## Business Architecture (RBA)

### End-to-End Process

Finance — Invoice to Cash

### Process Hierarchy

```
Finance (E2E)
└── Invoice to Cash
    └── Manage Customer Invoices (BPS-363)
        └── Invoice customer
        └── Manage 3rd party invoice
```

### Summary

The billing specialist's challenge maps to the Invoice to Cash E2E — specifically the Manage Customer Invoices sub-process — where billing data must be validated against contract payment terms to produce an accurate accounts receivable review.

## Fit Gap Analysis

| Requirement (business) | Standard asset(s) found | API ORD ID | MCP Server ORD ID | MCP Server Version | Webhook API ORD ID | Data Product ORD ID | Gap? | Notes / assumptions |
|------------------------|------------------------|------------|-------------------|--------------------|--------------------|---------------------|------|---------------------|
| Retrieve current customer billing information | Customer Billing Information (provided capability) | — | Provided via Customer Billing Information capability | — | — | — | No | Agent uses this capability directly; no new API integration built |
| Retrieve contractual payment terms | Contract Information (provided capability) | — | Provided via Contract Information capability | — | — | — | No | Agent uses this capability directly; no new API integration built |
| Identify discrepancies between billing behavior and contract terms | Agent reasoning over both capability outputs | — | — | — | — | — | No | Handled by agent LLM reasoning; no additional tool required |
| Produce Customer Billing Review Summary with justified recommendation | Agent output | — | — | — | — | — | No | Agent generates summary from retrieved data |
| Strict read-only enforcement | Agent design constraint | — | — | — | — | — | No | Agent must not expose or call any write operations |
| Decline out-of-scope requests | Agent guardrail | — | — | — | — | — | No | System prompt and routing logic enforce scope boundary |

### Key findings

- The agent relies exclusively on two provided capabilities: **Customer Billing Information** and **Contract Information**. No custom MCP tools or new API integrations are required.
- Scope routing is a core agent responsibility: billing-only, contract-only, and combined requests each follow a distinct retrieval path.
- The combined review path produces a **Customer Billing Review Summary** — a structured, justified output that is the primary deliverable.
- The agent is strictly read-only; no write, update, or transactional operations are permitted under any circumstances.
- Out-of-scope requests (e.g., applying credits, changing payment terms, initiating collections) must be actively declined with a clear message.
- SAP S/4HANA Cloud (Public or Private Edition) covers the underlying Billing Data Management and Customer Invoice Processing capabilities referenced in the standard RBA for this sub-process.

## Recommendations

### Read-only Customer Billing Review Agent

#### Executive Summary

Conversational AI agent that unifies billing and contract data for fast, accurate specialist reviews.

#### Recommended Solution

A pro-code Python AI agent (A2A protocol) deployed on SAP BTP. The agent connects to two pre-existing capabilities — Customer Billing Information and Contract Information — and applies LLM reasoning to classify each request, retrieve only the required data, identify discrepancies, and produce a Customer Billing Review Summary. The agent enforces strict read-only access and declines any out-of-scope requests.

#### Problem Statement

Billing specialists at Barton Industrial Supply must navigate two separate data sources — billing records and contract terms — manually, which takes approximately 20 minutes per customer review and is prone to inconsistencies. There is no consolidated view or automated comparison.

#### Affected User Roles

- Billing Specialist

#### Important factors

##### Reduces review time through unified data access

The agent retrieves billing and contract data in a single conversation, eliminating the need to switch between systems and reducing review time from ~20 minutes to ~5 minutes per customer.

##### Enforces read-only compliance by design

All write operations are excluded from the agent's tool set. The system prompt and routing logic prevent any modification of invoices, contracts, payment arrangements, or collections actions.

##### Scope-aware routing improves accuracy

The agent determines whether a request is billing-only, contract-only, or a full review, and retrieves only the relevant data. This prevents unnecessary data access and reduces cognitive load for the specialist.

#### Potential risks

##### Capability availability and data quality

The agent's output quality depends entirely on the data returned by Customer Billing Information and Contract Information. Incomplete or stale data in those capabilities will directly affect the accuracy of the review summary.

##### LLM reasoning consistency

The discrepancy identification and recommendation logic relies on LLM reasoning. Edge cases or ambiguous contract terms may produce inconsistent outputs and should be monitored through evaluation.

#### Recommended solution category

AI Agent

#### Intent fit
92%
