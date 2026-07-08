---
name: process-kpi-log
description: Log a KPI reading against a process and compute the trend versus baseline and target. Tracks two categories - process performance (cycle time, cost, error rate) and adoption/reuse (is the team actually using the change, are other areas adopting it, is it reusable). Use to measure whether a process optimization is actually working and sticking, so improvement is evidence-based rather than asserted.
user-invocable: true
allowed-tools: Read, Write, AskUserQuestion
---

You are recording a measurement against a process so its improvement can be proven over time. Output:
one appended KPI-log entry (validating against `kpi-schema.yaml`) plus a computed trend versus the
process's baseline and target. This closes the loop opened by `process-intake` (which captured the
baseline and desired outcome) - and it answers the only question that ultimately matters about a
process change: did it actually work, and did it stick?

## Why this skill exists

A process change is easy to declare "done" and hard to prove. Two things get skipped: measuring whether
performance actually moved against the baseline, and measuring whether the change was **adopted** - is
the team using it, did other areas pick it up, is it reusable? Without both, "we optimized it" is an
assertion, not a result. This skill makes each reading a small, auditable, dated entry tied to a
process, and computes the trend so the answer is a number, not a claim.

## Inputs

Invoke as:

```
/process-kpi-log <process-name-or-id> <reading>
```

- `<process-name-or-id>` - the process (ties back to its Process Intake Record).
- `<reading>` - either provided directly (metric, value, unit, date, source) or extractable from a
  status report / transcript passed as a file path or pasted text.

If baseline or target for the process is not on hand, read the process's intake record (if available)
to pull `baseline_metrics` and `desired_outcome`; otherwise ask for them or mark them unknown.

## Pipeline

### Step 1. Identify the process and its baseline/target

Resolve which process this reading belongs to. Pull its `baseline_value` and `target` from the intake
record where available so the trend can be computed. If neither is known, record the reading anyway and
note that trend is not yet computable.

### Step 2. Classify the reading

Decide which category it is:
- **Performance** - cycle time, cost, throughput, error/defect rate, rework rate.
- **Adoption / reuse** - adoption rate (share of the owning team actually using the new process),
  reuse (other teams/areas that adopted the same optimization), reusability assessment, stickiness
  (is adoption sustained or decaying).

### Step 3. Capture the entry

Populate the appropriate entry in `kpi-schema.yaml`. Every entry is tied to a process, dated
(`as_of_date`, absolute), and sourced. **Never fabricate a number** - if a value isn't measured, don't
record it; capture what is real.

### Step 4. Compute the trend

- Performance: compute change vs. the prior reading and vs. `baseline_value`; report direction
  (up / down / flat) and % change; compare against `target` and report % of the way to target.
- Adoption/reuse: report adoption_rate over time (rising / flat / decaying = the stickiness signal) and
  reuse_count growth.
Be explicit about direction meaning - for cycle time, "down" is good; for adoption_rate, "up" is good.
State which direction is favorable for each metric.

### Step 5. Emit outputs

1. The appended KPI-log entry (JSON per `kpi-schema.yaml`).
2. A one-line trend readout: "<metric>: <value> <unit> as of <date> - <direction> <n>% vs baseline,
   <m>% to target" (and the adoption equivalent).
3. If adoption is decaying or performance is regressing, flag it - that is a signal for the
   improve-and-scale step (see `../process-improve/VISION.md`), e.g. "adoption falling - the change may
   not be sticking; re-approach the owner."

## Guardrails

- **Never fabricate a number.** Record only measured readings.
- **Every reading is tied to a process, dated, and sourced** - that is what makes the log auditable.
- **Name the favorable direction per metric** so a reader can't misread "down" as bad (or good).
- **Adoption is a first-class metric, not an afterthought.** A performance win with zero adoption is not
  a win - surface both.

## Anti-patterns

- Don't report a performance gain without an adoption reading - the two together tell the truth.
- Don't compute a trend against a baseline you invented; if baseline is unknown, say so.
- Don't overwrite prior readings - the log is append-only so trends and audits hold.

## Triggers

Invoke when a new measurement is available for a process under optimization - a cycle-time number, a
cost figure, a usage/adoption stat, or a report you can extract one from.

## Done when

The reading is appended (validating against `kpi-schema.yaml`), the trend vs. baseline/target is
computed with the favorable direction named, and any regression or adoption decay is flagged.
