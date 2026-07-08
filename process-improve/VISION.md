# process-improve (roadmap / vision - not built in this demo)

The third skill closes the loop by turning the measured data from `process-kpi-log` back into two kinds
of improvement. It is process optimization applied reflexively - to the optimization work itself. It is
described here as a roadmap; the two working skills (`process-intake`, `process-kpi-log`) are the
foundation it would build on.

## Loop 1 - improve the tooling

Continuous improvement of the intake process itself, so the standard tightens over time:

- **Analyze recent intake records + reviewer feedback.** Which fields are chronically `missing` or
  `inferred`? Which questions do reviewers keep having to add by hand? Where does normalization drift
  (three spellings of the same department; inconsistent metric units)?
- **Propose changes** to the intake question set, the schema, and the normalization rules - e.g. "add a
  field for data-residency because it keeps showing up as an open question," or "these two pain
  categories keep getting conflated; split them," or "normalize business-unit names to a controlled
  list."
- **Human-approve, then apply,** and keep a changelog / audit trail of how the standard evolved. The
  intake gets smarter and more consistent without losing the governance guardrails.

**Concrete example.** If several intake records in a row leave *data residency* as an open question,
the improve step proposes adding a standard data-residency question to the intake, updating the schema
to capture it, and logging the change (with who approved it and when) after human review. The next
batch of intakes then captures data residency by default instead of surfacing it as a repeated gap -
the standard tightened itself, on evidence.

## Loop 2 - scale on adoption evidence

Use the adoption and reuse signals (not opinion) to decide what happens to each optimization:

- **Scale** - high `adoption_rate` + `reusability_assessment: yes` -> template it and take it wide
  (turn a one-team win into a repeatable enterprise capability).
- **Coach / re-approach** - performance improved but `adoption_rate` is low or `stickiness: decaying`
  -> the change isn't sticking. Find out why (owner, training, the "why" wasn't sold) and re-approach,
  rather than declaring victory on a change nobody uses.
- **Sunset** - not adopted and not reusable -> stop investing; retire it cleanly.

This is the difference between "we ran a pilot" and "we built something the organization actually uses
and can reuse."

## Why it matters

- **Adoption is the real success metric.** A process win with zero adoption is not a win. Making
  scale/coach/sunset an evidence-based decision keeps effort pointed at what sticks.
- **The system compounds.** Every engagement leaves the tooling a little better and the catalog of
  reusable, adopted optimizations a little bigger - so what the team can do six months from now is
  greater than what it can do today.
- **Governance stays in the loop.** Every improvement is human-approved and logged; scaling a process
  re-checks its governance flags at the new, wider scope. Enablement and control move together.

## Dependencies

- `process-intake` - supplies the structured records and the `extraction_confidence` signal that
  reveals where the questions/schema are weak.
- `process-kpi-log` - supplies the performance and adoption/reuse data that drives both loops.
