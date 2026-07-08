---
name: process-intake
description: Turn an ambiguous process-optimization request (an interview/intake-call transcript, a request email, or pasted text) into a standardized, database-ready Process Intake Record. Maps the process boundaries, flags governance exposure (SOX, PII, privileged, regulatory) up front, captures baseline metrics, identifies an accountable-owner candidate and local champion, drafts a change-management plan, and surfaces open questions instead of guessing. Understand-before-automate: the record is a draft for human review, not an autonomous system of record. Use at the start of any process-optimization engagement to bring structure to the ambiguity.
user-invocable: true
allowed-tools: Read, Write, WebFetch, AskUserQuestion
---

You are converting an ambiguous, unstructured process-optimization request into ONE standardized,
governed, database-ready record that a person can review, prioritize, and act on. Output: a Process
Intake Record (JSON validating against `intake-schema.yaml`), a short human-readable summary, and an
explicit list of open questions for anything that could not be determined from the input.

## Why this skill exists

Process-improvement work starts as ambiguity: a "can you make this faster?" email, or a business-unit
conversation where the real problem, the real numbers, and the real compliance exposure are all
implicit. Someone then has to manually read it, figure out what is actually being asked, guess at the
metrics, and remember to check whether it touches anything regulated - and that pass is slow,
inconsistent, and easy to skip. This skill makes it a fast, repeatable pass that produces something
concrete to react to, with governance flagged before any work starts and nothing fabricated.

## Understand before you automate (the method, not "AI-first")

This skill captures understanding; it does not jump to a solution. The discipline it enforces:

1. Understand the current workflow (what actually happens, and where it crosses team/system boundaries).
2. Ask why each major step exists - and what value it adds.
3. Identify waste, risk, and unclear ownership.
4. Confirm which controls are *required* (and must be preserved).
5. Only then improve or automate - after the process is understood, not before.
6. Measure whether the change worked and stuck (handed off to `process-kpi-log`).

Automation is an outcome of understanding, never the starting point.

## Inputs

Invoke as:

```
/process-intake <source>
```

`<source>` is one of:
- **File path** to a transcript or email export (read via `Read`).
- **URL** to a meeting transcript or shared doc (fetched via `WebFetch` with the prompt: "Extract the
  full text, preserving speaker labels and turn order").
- **Paste** - the transcript or email text as the rest of the prompt.

If the source is ambiguous, ask ONE clarifying question, then proceed. Detect the input type
(interview/intake-call transcript vs. email) and set `source_type` accordingly.

## Pipeline

### Step 1. Load and read the input

Resolve the source and read it in full. If it is a summary rather than a full transcript, work with
the summary and note the reduced granularity in the output.

### Step 2. Flag governance exposure FIRST

Before extracting anything else, scan for compliance exposure and populate the `governance` block. This
is deliberately first, not last - a process cannot be triaged safely until its exposure is known.
- **SOX controls** - does the process touch financial reporting, financial data, or an internal
  control (approvals over spend, revenue recognition, journal entries, vendor/customer master data,
  access to financial systems)? Name the control area if identifiable.
- **PII / privileged** - does it handle personal data (employee/customer PII, PHI), attorney-client or
  otherwise privileged material, or confidential/sensitive business data? Name the data type.
- **Other regulatory** - GDPR / data residency, industry/regulated-market rules, export control, AI
  governance (e.g. EU AI Act), or contractual constraints.
If exposure is unclear, mark it as a **flag to confirm** in `open_questions` - do NOT assume "none."

### Step 3. Extract the record fields

Populate every field in `intake-schema.yaml`. Be exhaustive but honest. For each field, decide whether
its value is **stated** (explicit in the input), **inferred** (reasonably deduced), or **missing**, and
record that in `extraction_confidence`. Key fields:
- `current_state_summary` - how the work is done today (the "understand" step).
- `baseline_metrics` - any time / cost / people / throughput numbers, each with its source. If the
  input has none, leave the list empty and add "capture baseline metrics" to `open_questions`.
- `pain_points` - the points of failure and waste.
- `approval_chain` - who approves, and how many steps. **If the approver count looks high, do not judge
  it - flag it** as a question ("why are there N approvers - what value does each add?"). Excess
  approvals are common low-hanging fruit, but the answer is found by asking, not assuming.
- `process_boundary` - the trigger, start and end points, upstream/downstream dependencies, systems
  touched, and especially the **handoff points** between teams. Many process failures happen between
  teams, not inside a single step - so capturing the seams matters.
- `accountable_owner_candidate` - the one person who could own the end-to-end process. If none is
  evident, set it to "none identified - needs assignment."
- `local_champion` - someone inside the requesting org willing to sponsor the change. Change rarely
  sticks without one; if absent, flag it.
- `change_management` - who is affected, what communication / training is needed, what resistance is
  likely, and the handoff + support plan. Adoption is *earned*; a design with no change plan is a
  design that won't stick.
- `quick_win_flag` - is there an obviously low-value step that is a candidate for elimination?
- `value_hypothesis` - the expected benefit in the terms leadership cares about (time saved, cost
  saved, quality, risk reduced). Frame as a hypothesis, not a promise.

Use absolute dates (resolve "last quarter" -> the actual quarter). Quote the source for any claim where
the exact wording matters.

### Step 4. Route ambiguity into open questions (do not guess)

Anything you could not determine, or any governance exposure you could not confirm, goes into
`open_questions` as a concrete, answerable clarification - phrased as an explicit deliverable question
("Confirm whether vendor banking details are stored in the spreadsheet" rather than "unclear about
data"). This is the mechanism that keeps a fast first draft trustworthy.

### Step 5. Emit outputs

1. **Process Intake Record** - JSON validating against `intake-schema.yaml`. Write to a file if a path
   was given, otherwise print it.
2. **Human-readable summary** - 5-8 lines: process, requester, current state, top pain, governance
   flags, proposed owner, suggested next step.
3. **Clarify these** - the `open_questions` list, so the requester can close the gaps.

Set `suggested_next_step` to one of: confirm scope with requester | assign an accountable owner |
run a quick-win pilot | schedule a deep-dive.

## Guardrails

- **Governance first, always.** Populate the `governance` block before anything else; never default it
  to "none" - unknown exposure is an `open_question`, not a "no."
- **Never fabricate.** Mark every field stated / inferred / missing. Empty is honest; invented is not.
- **Something to react to, not false precision.** The point is a fast, structured first draft with
  confidence flags - not a perfect record. Missing detail becomes an open question, not a guess.
- **Quote, don't paraphrase, where wording matters** (a "we'll table it" is not a "we'll drop it").
- **Human-in-the-loop.** This produces a proposed record for review; it does not write to any system of
  record on its own. The record's `review_audit.approval_status` starts at `draft`; a person reviews,
  approves, and the `change_log` + `source_evidence_refs` keep it auditable.

## Anti-patterns

- Don't infer a governance "clear" - absence of evidence is an open question, not a green light.
- Don't collapse distinct pain points into one - granularity preserves auditability and priority.
- Don't invent metrics to fill `baseline_metrics` - an empty baseline with a "capture this" question is
  more useful than a fabricated number.
- Don't assign an owner who wasn't identified - propose "needs assignment" instead.

## Triggers

Invoke when someone hands over a process-optimization request - a transcript, an email, or a verbal ask
written down - and wants it turned into something structured and actionable.

## Done when

The record validates against `intake-schema.yaml`, the governance block is populated (with unknowns as
open questions), the summary and "clarify these" list are produced, and nothing was fabricated.
