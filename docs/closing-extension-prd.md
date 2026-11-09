# Product Requirements Document (PRD)

**Title:** Stonebridge Equipment Group — Financial Closing Escalation Extension  
**Date:** 2026-11-09  
**Owner:** Stonebridge Equipment Group — Finance Transformation  
**Solution Category:** Agent Extension

---

## Product Purpose & Value Proposition

**Elevator Pitch:**
Stonebridge Equipment Group runs a complex period-end close across multiple legal entities involving hundreds of interdependent tasks. Finance teams currently spend up to two hours manually scanning tasks and documents to spot critical escalation situations — often too late to prevent reporting delays. This extension adds three company-specific escalation rules to the Financial Closing Assistant as instructions, reducing that identification time to under 15 minutes and delivering a prioritized, read-only advisory summary with no impact on existing agent capabilities.

**Business Need:**
The Financial Closing Assistant orchestrates financial close activities but applies no company-specific escalation logic. Without automated escalation, controllers and close managers must manually review task lists and posting logs across legal entities to identify high-risk situations — a process that is slow, inconsistent, and prone to oversight. The extension closes this gap by encoding Stonebridge's defined escalation rules directly into the agent's instruction layer.

**Expected Value:**
Time to identify and escalate critical financial closing issues reduced from 2 hours to 15 minutes per period-end close cycle, enabling faster intervention and reducing the risk of missed reporting deadlines.

**Product Objectives (Prioritized):**
1. Automatically classify closing issues according to Stonebridge's three escalation rules (Critical, Urgent, Flag) within every closing assistant interaction
2. Deliver a consolidated, prioritized escalation summary (Critical → Urgent → Flag) with actionable next steps for each identified item
3. Preserve all existing Financial Closing Assistant capabilities without modification or disruption

---

## Business Metrics

| Metric | Baseline | Target | Timeline | Process / Capability | Source |
|--------|----------|--------|----------|----------------------|--------|
| Time to identify and escalate critical financial closing issues (high-value posting errors, imminent close deadlines, overdue blocking tasks) | 2 hours | 15 minutes | Per period-end close | Financial Period-End Closing / Escalation Management | user |

---

## User Profiles & Personas

### Primary Persona: Financial Controller

Maria is a 42-year-old Financial Controller responsible for period-end close across four legal entities in Western Europe. During close week she monitors hundreds of open tasks and reviews posting logs for errors, often across two systems simultaneously. She is highly proficient with SAP S/4HANA and Advanced Financial Closing but spends significant time triaging issues manually because the system provides no consolidated view of what is truly critical right now. She needs to know — within minutes — which issues require her direct intervention, which are approaching a tipping point, and which are at schedule risk, so she can allocate her limited attention effectively.

### Secondary Persona: Period-End Close Manager

James is a 38-year-old Period-End Close Manager who owns the master closing schedule for Stonebridge's industrial equipment division. He coordinates task assignments across accounting teams and escalates to senior finance leadership when timelines are at risk. His core frustration is that he learns about deadline-critical situations too late — often after a task has already caused a downstream dependency failure. He needs an advisory tool that surfaces time-sensitive risks proactively so he can act before the window closes.

### Other User Types

- **Accounting Team Lead**: Responsible for task completion within their domain (GL, AR, AP, asset accounting). Needs to know which of their overdue tasks are blocking others so they can reprioritize.

---

## User Goals & Tasks

### For Financial Controller:

**Goals:**
- Identify all critical and urgent closing issues within minutes of querying the assistant
- Receive actionable guidance for each escalation item, not just a status

**Key Tasks:**
- Query the Financial Closing Assistant for the current escalation status during close
- Review the prioritized escalation summary and act on Critical items first
- Verify that all multi-rule items display all applicable escalation conditions

### For Period-End Close Manager:

**Goals:**
- Stay ahead of deadline-imminent risks before they become missed deadlines
- Maintain schedule integrity across legal entities

**Key Tasks:**
- Request an escalation summary from the assistant at regular intervals during close week
- Use the Urgent category to reprioritize resource allocation before the 4-hour threshold is breached

---

## Product Principles

1. **Read-only, always**: The extension observes and advises; it never writes, posts, approves, or modifies. Any action on an escalated item is performed by a human, outside the assistant.
2. **Rules as stated**: The three escalation rules — EUR 100,000 threshold, 4-hour deadline proximity, overdue-blocking-dependency — are applied exactly as specified. No approximation, inference, or softening.
3. **Additive, not disruptive**: The extension adds escalation intelligence to the Financial Closing Assistant without replacing, overriding, or degrading any of its existing capabilities.
4. **Clarity over completeness**: Each escalation entry must include the reference, the reason, and a recommended next step. A useful, actionable summary is more valuable than an exhaustive but unreadable one.

---

## Business Context

**Current State:**
Finance teams at Stonebridge manually monitor closing tasks and posting logs across multiple legal entities during period-end close. There is no automated mechanism to classify issues by severity or produce a prioritized escalation view. Critical situations (high-value posting errors, tasks approaching deadlines, overdue blockers) are identified reactively, often after the optimal intervention window has passed.

**Strategic Alignment:**
Reducing the time to identify financial close risks directly supports Stonebridge's commitment to accurate, on-time financial reporting across its multi-entity structure. The extension leverages the existing Financial Closing Assistant investment and applies company-specific business rules without additional integration overhead.

---

## Goals and Non-Goals

### Goals (In Scope)

- Encode three escalation rules as instructions in the Financial Closing Assistant's Default capability extension point
- Produce a consolidated escalation summary ordered Critical → Urgent → Flag with task/document reference, escalation reason, and recommended next steps per entry
- Detect and display all applicable escalation conditions when a single item satisfies multiple rules
- Preserve all existing Financial Closing Assistant capabilities (journal entry, accruals, clearing, insights)

### Non-Goals (Out of Scope)

- Posting documents, completing tasks, approving entries, or modifying the closing schedule
- Deploying the extension to any environment
- Adding new API integrations, MCP tools, hooks, or workflow automation
- Generating business metrics or forecasts beyond the escalation summary
- Replacing or modifying the base Financial Closing Assistant agent

---

## Requirements

### Must-Have Requirements

**REQ-01**: High-Value Posting Error Classification

- **Problem to Solve**: Finance teams cannot quickly identify which posting errors are material enough to require immediate intervention during close.
- **User Story**: As a Financial Controller, I need closing documents with posting errors exceeding EUR 100,000 to be automatically classified as "Critical - Immediate Action Required" so that I can prioritize them above all other closing issues.
- **Acceptance Criteria**:
  - Given a closing document with a posting error and an amount exceeding EUR 100,000, when the escalation summary is generated, then the document is listed under "Critical - Immediate Action Required" with its reference, the posting error reason, and recommended next steps.
  - Given a posting error with an amount of EUR 100,000 or less, when the escalation summary is generated, then it is not classified as Critical under this rule.
- **Maps to Objective**: Objective 1
- **Priority Rank**: 1

**REQ-02**: Close Deadline Proximity Classification

- **Problem to Solve**: Incomplete closing tasks approaching the period-end deadline are not surfaced until they have already caused a delay.
- **User Story**: As a Period-End Close Manager, I need incomplete closing tasks with fewer than four hours remaining before the official period-end deadline to be classified as "Urgent - Deadline Imminent" so that I can reallocate resources before the deadline is missed.
- **Acceptance Criteria**:
  - Given an incomplete closing task with fewer than four hours remaining to the period-end deadline, when the escalation summary is generated, then the task is listed under "Urgent - Deadline Imminent" with its reference, the time remaining, and recommended next steps.
  - Given an incomplete task with four or more hours remaining, when the escalation summary is generated, then it is not classified as Urgent under this rule.
- **Maps to Objective**: Objective 1
- **Priority Rank**: 2

**REQ-03**: Overdue Blocking Task Escalation

- **Problem to Solve**: Overdue tasks that are blocking downstream dependencies are not distinguished from overdue tasks with no downstream impact, causing misplaced escalation effort.
- **User Story**: As an Accounting Team Lead, I need closing tasks that have passed their scheduled completion time and are blocking downstream dependencies to be classified as "Flag - Schedule Risk" so that I can address sequence-breaking delays before they cascade.
- **Acceptance Criteria**:
  - Given a closing task that is past its scheduled completion time and has at least one blocked downstream dependency, when the escalation summary is generated, then the task is listed under "Flag - Schedule Risk" with its reference, the overdue duration, the blocked dependency, and recommended next steps.
  - Given an overdue task with no downstream dependencies blocked, when the escalation summary is generated, then it is not classified as Flag under this rule.
- **Maps to Objective**: Objective 1
- **Priority Rank**: 3

**REQ-04**: Consolidated Prioritized Escalation Summary

- **Problem to Solve**: Without a structured, ordered view, users cannot efficiently triage multiple escalation signals during a time-pressured close.
- **User Story**: As a Financial Controller, I need a single consolidated escalation summary ordered Critical → Urgent → Flag, with each entry showing the task or document reference, the escalation reason, and recommended next steps, so that I can triage all issues in one interaction.
- **Acceptance Criteria**:
  - When the escalation summary is produced, Critical items appear first, followed by Urgent, followed by Flag.
  - Each entry includes: the task or document reference, the specific escalation reason (referencing the applicable rule), and recommended next steps.
  - If no items qualify under a tier, that tier is omitted from the summary or noted as clear.
- **Maps to Objective**: Objective 2
- **Priority Rank**: 4

**REQ-05**: Multi-Rule Item Detection

- **Problem to Solve**: A single closing item may satisfy more than one escalation rule; displaying only the highest severity misses important context for the user.
- **User Story**: As a Financial Controller, I need items that satisfy multiple escalation rules to display all applicable conditions in a single summary entry so that I have complete context for my response.
- **Acceptance Criteria**:
  - Given a closing document or task that satisfies two or more escalation rules, when the escalation summary is generated, then all applicable rule classifications and reasons are shown within the same entry.
  - The entry is positioned at the highest applicable severity tier (e.g., an item that is both Critical and Urgent appears in the Critical section with both conditions noted).
- **Maps to Objective**: Objectives 1 and 2
- **Priority Rank**: 5

**REQ-06**: Preservation of Existing Financial Closing Assistant Capabilities

- **Problem to Solve**: An extension that disrupts existing agent capabilities would reduce trust and utility.
- **User Story**: As any user of the Financial Closing Assistant, I need all existing capabilities — journal entry creation, accruals processing, receivables and payables clearing, and financial insights — to remain fully available and unaffected so that the extension does not degrade my existing workflows.
- **Acceptance Criteria**:
  - All existing Financial Closing Assistant capabilities continue to operate as before the extension.
  - The escalation instruction layer does not override, restrict, or alter any base agent response outside of the escalation summary.
- **Maps to Objective**: Objective 3
- **Priority Rank**: 6

---

## Solution Architecture

**Architecture Overview:**
The solution is a single agent extension asset targeting the Financial Closing Assistant (`sap.agtcloass:agent:financial-closing-assistant:v1`). It uses the supported Default capability instruction extension point — the only extension mechanism the base agent exposes. No new components, integrations, hooks, or tools are introduced.

**Key Components:**
- **Agent Extension asset**: Contains `asset.yaml` and `extension.yaml`; the `asset.yaml` declares a dependency on the base agent via its ORD ID and version
- **Extension instructions**: The `extension.yaml` encodes the three escalation rules, the escalation summary format, the prioritization order, multi-rule detection logic, and the read-only advisory constraint

**Integration Points:**
- None — the extension relies entirely on data already accessible to the base Financial Closing Assistant through its existing capabilities

### Automation & Agent Behaviour

**Automation Level:** Rule-based (instruction-driven, no autonomous action)

**Actions the system performs without human approval:**
- Classifies closing documents and tasks against the three escalation rules
- Generates the prioritized escalation summary

**Actions that require human review or approval:**
- All responses to escalation items — the extension is advisory only; no action is taken automatically

**Model or engine used:** Financial Closing Assistant base agent (SAP Generative AI Hub, as configured in the base agent)

**Knowledge & data sources accessed:**
- Closing task status, scheduled completion times, and dependency chains — accessed through the base agent's existing capabilities (SAP Advanced Financial Closing)
- Posting documents and error details — accessed through the base agent's existing capabilities (SAP S/4HANA)

**Tools or connectors invoked:**
- None — instruction-only extension; no new tools or connectors are introduced

**Guardrails & fail-safes:**
- The extension must never post documents, complete tasks, approve entries, or modify the closing schedule
- If data required to evaluate a rule is unavailable or ambiguous, the extension must state this clearly in the summary rather than infer a classification
- The extension must not suppress, override, or alter any base agent response unrelated to the escalation summary

---

## Milestones

### M1: Extension Generated

- **Description**: The instruction-based extension asset is created with all three escalation rules encoded and all extension files in place; no deployment is performed
- **Achieved when**: `asset.yaml` and `extension.yaml` are present, syntactically valid, and reference the base agent correctly; the escalation rules, summary format, and read-only constraint are fully encoded in the instructions
- **Log on achievement**: `M1.achieved: instruction-based extension generated with all three escalation rules; no deployment performed`
- **Log on miss**: `M1.missed: extension generation incomplete — one or more required files or rule definitions are missing`

### M2: Critical Rule Encoded

- **Description**: The rule classifying posting errors above EUR 100,000 as "Critical - Immediate Action Required" is present and correctly specified in the extension instructions
- **Achieved when**: The extension instructions include the EUR 100,000 threshold, the "Critical - Immediate Action Required" classification label, and the requirement to include document reference, reason, and recommended next steps
- **Log on achievement**: `M2.achieved: Critical escalation rule (EUR 100,000 posting error threshold) encoded in extension instructions`
- **Log on miss**: `M2.missed: Critical rule missing or threshold incorrectly specified in extension instructions`

### M3: Urgent Rule Encoded

- **Description**: The rule classifying incomplete tasks with fewer than four hours to the period-end deadline as "Urgent - Deadline Imminent" is present and correctly specified
- **Achieved when**: The extension instructions include the sub-4-hour threshold, the "Urgent - Deadline Imminent" classification label, the incomplete-task condition, and the requirement to include task reference, time remaining, and recommended next steps
- **Log on achievement**: `M3.achieved: Urgent escalation rule (sub-4-hour deadline proximity) encoded in extension instructions`
- **Log on miss**: `M3.missed: Urgent rule missing or deadline threshold incorrectly specified in extension instructions`

### M4: Flag Rule Encoded

- **Description**: The rule classifying overdue tasks blocking downstream dependencies as "Flag - Schedule Risk" is present and correctly specified
- **Achieved when**: The extension instructions include the overdue-and-blocking condition, the "Flag - Schedule Risk" classification label, and the requirement to include task reference, overdue duration, blocked dependency, and recommended next steps
- **Log on achievement**: `M4.achieved: Flag escalation rule (overdue blocking task) encoded in extension instructions`
- **Log on miss**: `M4.missed: Flag rule missing or blocking-dependency condition incorrectly specified in extension instructions`

### M5: Prioritized Escalation Summary Specified

- **Description**: The extension instructions define a consolidated summary output ordered Critical → Urgent → Flag, with multi-rule detection and full entry content requirements
- **Achieved when**: The extension instructions specify the Critical → Urgent → Flag ordering, multi-rule item handling (all applicable conditions shown at the highest applicable tier), and per-entry content requirements (reference, reason, recommended next steps)
- **Log on achievement**: `M5.achieved: prioritized escalation summary output fully specified in extension instructions`
- **Log on miss**: `M5.missed: escalation summary format, ordering, or multi-rule handling not fully specified in extension instructions`

---

## Risks, Assumptions, and Dependencies

### Risks

- **Data availability at query time**: The escalation rules depend on task status, deadline times, posting error details, and dependency chain data being accessible in the agent's context at the time of the query. If the base agent's data access is incomplete or stale, escalation accuracy may be reduced.
- **Rule boundary ambiguity**: Edge cases (e.g., a task exactly at the 4-hour boundary, or a posting error exactly at EUR 100,000) must be handled consistently. The rules as specified use strict thresholds ("exceeding", "fewer than"); the instructions must reflect this precisely.

### Assumptions (Validate These)

- The Financial Closing Assistant's Default capability instruction extension point is sufficient to encode all three rules and produce the escalation summary output as described
- The base agent already has access to the task status, deadline, dependency, posting error, and amount data required to evaluate the rules
- The instruction extension does not require hooks, tools, or API integrations beyond what the base agent already provides

### Dependencies

- Financial Closing Assistant (`sap.agtcloass:agent:financial-closing-assistant:v1`, version 1.0.0) must be available in the target tenant as the base agent for this extension

---

## Appendix

### Glossary

- **AFC**: SAP Advanced Financial Closing — the SAP product that manages closing task scheduling, dependencies, and status tracking
- **Period-end close**: The set of activities performed at the end of an accounting period to finalize financial records across all legal entities
- **Escalation summary**: The prioritized, read-only advisory output produced by this extension, listing all items classified under the three escalation rules
- **Instruction extension point**: The mechanism supported by the Financial Closing Assistant's Default capability that allows additional agent instructions to be injected without modifying the base agent

### References

- Financial Closing Assistant: `sap.agtcloass:agent:financial-closing-assistant:v1`
- SAP Advanced Financial Closing (AFC): Local Financial Closing, Group Financial Closing capabilities
- SAP S/4HANA: General Ledger Accounting, Financial Analytics capabilities
- RBA sub-process: Perform accounting and financial close (BPS-413_002)
