# Worked example: refund / credit issuance (end to end)

A single synthetic scenario run through both skills, start to finish, to show the whole loop on one
process. Everything here is invented - no real organization, data, or numbers.

## The flow

1. **Input** - `input-refund-approval-email.md`: a messy, ambiguous request email ("can we PLEASE speed
   up customer refunds?").
2. **Intake** - `output-refund-approval-record.json`: what `process-intake` produces from that email. Note
   that it:
   - flags governance **first** - and catches four separate exposures from throwaway phrases: SOX (the
     `$500` approval threshold is a spend control), possible PCI, GDPR/EU data residency ("a couple went
     to the EU"), and contractual terms ("enterprise customers under contract");
   - **does not fabricate a baseline** - the anecdotal "a few hundred/month" and "a week" are marked
     `inferred` and exact capture is pushed into `open_questions`;
   - flags the quick win (agent self-service credits) but **gates it** on confirming which approvals are
     required controls;
   - emits 8 concrete "clarify these" questions and starts at `approval_status: draft` for human review.
3. **Measure** - `output-refund-kpi-log.json`: what `process-kpi-log` produces after the pilot ran. Cycle
   time falls from a 7-day baseline to 1.4 days (80% vs baseline, 93% to the 1-day target), adoption
   reaches 86% and a second team reuses the pattern - so it surfaces a **Scale** recommendation, while
   explicitly noting the required SOX/controller control was retained and only a duplicated pre-check was
   removed.

The point: structure the ambiguity, surface risk before solutioning, measure whether the change actually
worked and was adopted - speed with the controls intact.
