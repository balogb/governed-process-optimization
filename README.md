# Process-Optimization Skill Suite (demonstration)

A small, self-contained set of AI skills that show a repeatable, governed way to run enterprise
process optimization - from a messy incoming request all the way through to measured adoption. Built by
Brent Balog as a demonstration of approach. Everything here is generic and uses synthetic examples; it
is not deployed anywhere and contains no confidential or real data.

> **Why this matters.** I built this as a generic, synthetic example to show how I think about process
> transformation - it is not based on any organization's internal information. The point is the
> operating model, not the code: turn an ambiguous request into a reviewable record, surface risk
> early, establish a baseline, assign clear ownership, plan for adoption, and measure whether the
> change actually works and sticks - speed and structure without losing control, trust, or
> auditability. Consider it a conversation starter about *how* the work gets done, not a product or a
> proposed solution to anyone's specific process.

## The problem it addresses

Process-optimization work almost always starts as an **ambiguous ask** - a "can you make this faster?"
email, or a business-unit conversation where the real problem, the real metrics, and the real
compliance exposure are all implicit. Before you can help, you have to turn that ambiguity into
something concrete you can act on, prioritize, and govern.

## The method (a repeatable loop)

1. **Understand** what the process actually does today.
2. **Baseline** the metrics (time, cost, people, throughput).
3. **Find** the points of failure and waste.
4. **Optimize** so the improvement is measurable - and **make sure it's adopted and reusable.**

Automation follows understanding, never the reverse: you only improve or automate a step *after* you
understand why it exists and which controls it must preserve.

These skills operationalize that loop:

| Stage | Skill | What it does |
| --- | --- | --- |
| **Intake** - structure the ambiguity | `process-intake` (working) | Turns a transcript or an email into a standardized, DB-ready **Process Intake Record**, with governance (SOX / PII / privileged) flagged up front and open questions surfaced instead of guessed. |
| **Measure** - performance + adoption | `process-kpi-log` (working) | Logs KPI readings against a process and computes the trend vs. baseline and target - tracking both **process performance** and **adoption / reuse**. |
| **Improve & Scale** | `process-improve` (vision - see `process-improve/VISION.md`) | Uses the measured data to continuously improve the intake tooling itself and to make evidence-based scale / coach / sunset decisions. |

## Two principles woven through all of it

- **Enablement AND governance, not either/or.** Anyone can raise a process idea and get a fast,
  structured first draft to react to - but compliance exposure (SOX, PII, privileged, regulatory) is
  flagged at intake, every record is structured / sourced / dated for audit, and a human reviews
  before anything enters a system of record. The goal is to let people move fast *without* losing
  control of governance.
- **Never fabricate.** The intake skill marks each field as *stated*, *inferred*, or *missing*, and
  routes ambiguity into an explicit "clarify these" list rather than inventing detail. That is what
  makes a fast first draft trustworthy.

## How this maps to enterprise process-transformation work

The capabilities enterprise process-transformation work needs, and where this demo shows them:

| Capability | Where it shows up |
| --- | --- |
| Structure ambiguous intake | `process-intake` turns an email or transcript into a reviewable record |
| Process boundaries & handoffs | Intake maps trigger, start/end, dependencies, systems, and cross-team handoffs |
| Baseline KPI definition | Intake captures cycle time, volume, effort, rework, and desired outcomes |
| Governance & controls | Governance (SOX / PII / privileged / required controls) is populated *before* solutioning |
| Ownership clarity | Intake identifies an accountable-owner candidate and a local champion |
| Change management | Intake drafts stakeholders, communications, training, resistance, and handoff plan |
| Adoption & sustainability | `process-kpi-log` tracks adoption, reuse, and stickiness - not just delivery |
| Reusable patterns / scale | Reusability assessment feeds evidence-based scale / coach / sunset decisions |
| Executive-ready measurement | The log rolls up into a simple leadership dashboard / scorecard (described below) |
| Continuous improvement | `process-improve` refines the standard itself and scales what works |

## Presenting to stakeholders and leadership

The same KPI-log data rolls up naturally into a simple dashboard or scorecard for stakeholders and
leadership - one line per process (baseline, current, target, adoption, governance status, owner, and a
recommended action such as scale, coach, or sunset). The point is that a single measurement discipline
serves both the working team, through the granular and auditable log, and leadership, through an
at-a-glance view whose recommendations are driven by adoption evidence rather than opinion.

## Files

```
process-intake/
  SKILL.md              the intake skill
  intake-schema.yaml    the Process Intake Record schema
  examples/             synthetic transcript + email inputs and their JSON outputs
process-kpi-log/
  SKILL.md              the KPI-log skill
  kpi-schema.yaml       KPI-log entry schema (performance + adoption/reuse)
  examples/             synthetic KPI log with computed trends
process-improve/
  VISION.md             the improve-and-scale skill, described as a roadmap
```

## How to run (Claude Code)

Drop `process-intake/` or `process-kpi-log/` into a skills directory and invoke by name, feeding a
transcript, an email, or a reading. Each `SKILL.md` documents its inputs and output. The `examples/`
folders show representative inputs and the exact structured output they produce.
