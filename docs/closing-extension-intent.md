# Stonebridge Equipment Group — Financial Closing Escalation Extension

Financial Closing Assistant extension adding company-specific closing escalation rules for Stonebridge Equipment Group.

## Business challenge

Stonebridge Equipment Group is an industrial equipment manufacturer operating across multiple legal entities with a complex period-end closing process involving hundreds of interdependent tasks, reconciliations, and document postings. The Financial Closing Assistant currently lacks company-specific escalation intelligence to identify critical closing issues, prioritize time-sensitive tasks, and prevent delays that could affect financial reporting deadlines. Finance controllers and close managers must manually monitor hundreds of tasks across entities to spot high-risk situations, which takes hours and frequently results in missed escalations. The extension must remain strictly read-only and advisory — it must not post documents, complete tasks, approve entries, or modify the closing schedule.

## Business Goals & Success Criteria

| Metric | Baseline | Target | Timeline | Process / Capability | Source |
|--------|----------|--------|----------|----------------------|--------|
| Time to identify and escalate critical financial closing issues (high-value posting errors, imminent close deadlines, overdue blocking tasks) | 2 hours | 15 minutes | Per period-end close | Financial Period-End Closing / Escalation Management | user |

## Key Milestones

| Milestone | Condition |
|-----------|-----------|
| Extension deployed | Agent extension is live and the Financial Closing Assistant responds with escalation summaries |
| Critical rule active | Posting errors > EUR 100,000 are correctly classified as "Critical - Immediate Action Required" |
| Urgent rule active | Incomplete tasks < 4 hours from deadline are correctly classified as "Urgent - Deadline Imminent" |
| Flag rule active | Overdue tasks blocking downstream dependencies are correctly classified as "Flag - Schedule Risk" |
| Prioritized summary delivered | Consolidated escalation output is ordered Critical → Urgent → Flag with task/document reference, reason, and next steps |

## Business Architecture (RBA)

### End-to-End Process

Record to Report — manufacturing variant (Record to Report with central finance)

### Process Hierarchy

```
Record to Report (E2E)
└── Record to Report
    └── Perform accounting and financial close (BPS-413_002)
        └── Perform financial closing
```

### Summary

Stonebridge's challenge maps to the Record to Report E2E process, specifically the "Perform accounting and financial close" sub-process, which covers multi-legal-entity closing orchestration, closing task management, posting error identification, deadline-driven prioritization, and dependency tracking.

## Fit Gap Analysis

| Requirement (business) | Standard asset(s) found | API ORD ID | MCP Server ORD ID | MCP Server Version | Webhook API ORD ID | Data Product ORD ID | Gap? | Notes / assumptions |
|------------------------|------------------------|------------|-------------------|--------------------|--------------------|---------------------|------|---------------------|
| Company-specific closing escalation rules (3-tier: Critical, Urgent, Flag) | None — no standard product provides rule-based escalation prioritization out of the box | — | — | — | — | — | Yes | Requires custom instruction extension on Financial Closing Assistant |
| High-value posting error detection (> EUR 100,000 → Critical) | SAP S/4HANA (posting error capture), SAP Advanced Financial Closing | — | — | — | — | — | Partial | Standard products capture errors; escalation classification and prioritization logic is custom |
| Deadline proximity alert (< 4 hours → Urgent) | SAP Advanced Financial Closing (task scheduling) | — | — | — | — | — | Partial | AFC tracks deadlines; sub-4-hour urgency classification and advisory output is custom |
| Overdue blocking task escalation (Flag - Schedule Risk) | SAP Advanced Financial Closing (dependency management) | — | — | — | — | — | Partial | AFC tracks dependencies; escalation flag and recommended next steps are custom |
| Consolidated prioritized escalation summary (Critical → Urgent → Flag) | None | — | — | — | — | — | Yes | Fully custom — delivered via instruction extension on Financial Closing Assistant |
| Preserve all existing Financial Closing Assistant capabilities | Financial Closing Assistant (sap.agtcloass:agent:financial-closing-assistant:v1) | — | — | — | — | — | No | Instruction-based extension does not replace or override existing agent behavior |

### Key findings

- The Financial Closing Assistant (`sap.agtcloass:agent:financial-closing-assistant:v1`) supports an instruction-based extension point ("Default" capability) — this is the only supported extension mechanism and is sufficient for this use case.
- No hooks, new tools, or new API integrations are required; all three escalation rules are implemented purely as agent instructions.
- SAP Advanced Financial Closing (AFC) and SAP S/4HANA provide the underlying data (task status, deadlines, posting errors, dependencies) that the agent already has access to through its existing capabilities.
- The extension is strictly read-only and advisory — it classifies and summarizes; it does not act.
- No MCP servers are available for the relevant APIs (Ledger - Read, Budget Period A2X), but these are not needed given the instruction-only approach.

## Recommendations

### Instruction-Based Escalation Extension for Financial Closing Assistant

#### Executive Summary

Extend the Financial Closing Assistant with company-specific escalation instructions for Stonebridge Equipment Group.

#### Recommended Solution

Implement an agent extension (instruction-based only) on the Financial Closing Assistant (`sap.agtcloass:agent:financial-closing-assistant:v1`) that encodes Stonebridge's three escalation rules as agent instructions using the supported "Default" capability extension point. The extension adds a consolidated, prioritized closing escalation summary to the assistant's advisory output:

- **Rule 1 — Critical**: Closing documents with posting errors involving amounts exceeding EUR 100,000 are classified "Critical - Immediate Action Required"
- **Rule 2 — Urgent**: Incomplete closing tasks with fewer than four hours remaining before the official period-end deadline are classified "Urgent - Deadline Imminent"
- **Rule 3 — Flag**: Closing tasks that have passed their scheduled completion time and are blocking downstream dependencies are classified "Flag - Schedule Risk"

Output: a consolidated escalation summary ordered Critical → Urgent → Flag. Each entry includes the task or document reference, escalation reason, and recommended next steps. Items matching multiple rules show all applicable conditions. No document posting, task completion, approval, or schedule modification is performed.

#### Affected User Roles

- Financial Controllers (multi-entity)
- Period-End Close Managers
- Accounting Team Leads

#### Important factors

##### Instruction-only extension preserves all existing capabilities

The Financial Closing Assistant's existing journal entry, accruals, clearing, and insight capabilities are fully preserved. The instruction extension point adds behavior without replacing or overriding anything.

##### No new integrations required

All data needed to apply the escalation rules (posting errors, task deadlines, completion status, dependency chains) is already accessible through the agent's existing capabilities backed by SAP S/4HANA and SAP Advanced Financial Closing.

#### Potential risks

##### Data availability at query time

The escalation rules depend on real-time task and posting data. If the underlying data is stale or incomplete in the agent's context, escalation accuracy may be reduced.

#### Recommended solution category

Agent Extension

#### Intent fit

95%
