# Run 1 - Initial Agent Prompt

> **Note:** This document is a reconstruction of the Run 1 instruction from the recorded task setup and Run 1 trajectory. It is preserved as evaluation evidence and is not presented as a verbatim transcript of the original prompt.

## Goal

Review all synthetic biomedical equipment maintenance reports and produce an auditable triage classification for every report.

For each report, determine:

- equipment type;
- issue type;
- urgency;
- assigned team;
- confidence score from 0.00 to 1.00;
- concise evidence-based reason;
- missing information where applicable.

## Sources to Use

Use the following sources:

1. `data/01_maintenance_reports.csv`
2. `data/02_equipment_register.xlsx`
3. `reference/03_maintenance_policy.pdf`
4. `reference/04_triage_examples.pdf`

The maintenance reports must be cross-referenced against the equipment register by `equipment_id`.

Use the maintenance policy as the primary decision standard. Use the triage examples only to understand the expected output structure and reasoning style.

## Source Boundaries

Do not use:

- `reference/05_irrelevant_document.pdf`
- anything under `private/`
- prior-run outputs or evaluation artifacts

Do not treat missing register fields as facts.

Do not invent equipment history, technical causes, patient impact, maintenance status, or other missing information.

Distinguish reported observations from confirmed technical causes.

## Required Analysis

Process all 14 maintenance reports.

For every report:

1. Match the `equipment_id` to the equipment register.
2. Confirm whether the equipment type agrees with the register.
3. Apply the maintenance policy to determine urgency and routing.
4. Consider previous reports involving the same equipment where applicable.
5. Identify missing information that materially affects the decision.
6. Assign a confidence score reflecting the available evidence.
7. Explain the classification using evidence from the supplied sources.

Pay particular attention to:

- repeated equipment IDs;
- incomplete maintenance reports;
- clinical context;
- uncertainty;
- conflicts between report statements and register information.

When information is missing, do not fill the gap by assumption. State what is missing and lower confidence where appropriate.

## Output

Produce a complete triage table covering all 14 reports with at least these fields:

| Report ID | Equipment ID | Equipment Type | Issue Type | Urgency | Assigned Team | Confidence | Reason | Missing Information |
|---|---|---|---|---|---|---:|---|---|

Also produce a trajectory/evidence record describing:

- files inspected;
- inspection order;
- important cross-references;
- decisions requiring interpretation;
- uncertain or incomplete cases;
- source conflicts;
- deliberately excluded material;
- any workspace or file-format problems discovered.

## Quality Standard

A good result must:

- process all 14 reports;
- cross-reference every equipment ID;
- follow the maintenance policy;
- distinguish evidence from assumptions;
- identify uncertainty;
- avoid inventing missing facts;
- consider repeated equipment history;
- provide confidence scores;
- use only permitted sources;
- ignore the intentionally irrelevant document;
- protect private evaluation material;
- preserve the source files without modification.

The final output should be auditable: another reviewer should be able to trace each classification back to the supplied report, register, policy, or example material.