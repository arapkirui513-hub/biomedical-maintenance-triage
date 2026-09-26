# Biomedical Maintenance Triage — Final Capstone Evaluation

## 1. Executive Summary

This capstone evaluated an AI agent performing a real multi-step professional-style task: triaging synthetic biomedical equipment maintenance reports using an equipment register, a maintenance policy, triage examples, and controlled data-quality context.

The evaluation was conducted across three deliberate runs.

The iteration followed this sequence:

1. **Run 1:** establish baseline behavior and diagnose failures.
2. **Run 2:** correct workspace/data-quality problems and clarify two urgency decision boundaries.
3. **Run 3:** clarify the team-routing ownership boundary exposed by Run 2.

The three-run process produced a traceable cycle of:

> baseline → diagnosis → targeted intervention → rerun → observed behavioral change → diagnosis

The final run processed all 14 reports while preserving source integrity, repeated-equipment handling, incomplete-information handling, confidence scoring, and private-data protection.

No further run was performed because the major workspace and policy ambiguities identified during the evaluation had been deliberately addressed, and the remaining MR-004 issue represents an explicit evidence boundary rather than a demonstrated workspace failure.

---

## 2. Task Definition

The agent's task was to review synthetic biomedical equipment maintenance reports and produce an auditable triage classification for every report.

Each classification required:

- report ID;
- equipment ID;
- equipment type;
- issue type;
- urgency;
- assigned team;
- confidence score;
- reason;
- missing information where applicable.

The agent was required to:

- inspect all 14 reports;
- cross-reference every equipment ID against the equipment register;
- apply the maintenance policy;
- consider prior reports involving the same equipment;
- avoid inventing missing information;
- identify uncertainty;
- ignore an intentionally irrelevant reference document;
- avoid accessing the private expected-results file;
- preserve source files.

---

## 3. Workspace and Context Design

### Agent-facing sources

The controlled workspace contained:

- `data/01_maintenance_reports.csv`
- `data/02_equipment_register.xlsx`
- `reference/03_maintenance_policy.pdf`
- `reference/04_triage_examples.pdf`
- `reference/05_irrelevant_document.pdf`
- `reference/06_data_quality_notes.md`

### Private evaluation material

The private expected-results file was kept outside the agent's permitted inputs:

- `private/01_expected_results_private.csv`

The private directory was excluded from Git.

### Evidence

Run artifacts were stored separately:

```text
evidence/
├── run-1/
├── run-2/
└── run-3/
```

This separation allowed the experiment to preserve prior outputs without allowing them to become evaluation inputs.

---

## 4. Run 1 — Baseline

### Baseline behavior

Run 1 successfully processed all 14 reports and demonstrated several desirable behaviors:

- all report IDs were processed;
- equipment IDs were cross-referenced;
- repeated equipment was recognized;
- incomplete reports were handled with explicit missing information;
- confidence scores were provided;
- the irrelevant document was not used;
- private expected results were not accessed.

### Problems diagnosed after Run 1

Four material issues were identified.

#### 4.1 MR-004 — stable patient / alternative monitoring ambiguity

Run 1 classified MR-004 as High.

The issue was not a missing file or parsing problem. The policy did not explicitly define how a stable patient with a working alternative monitoring method should be distinguished from a case presenting an immediate safety concern.

**Diagnosis:** instruction/policy ambiguity.

#### 4.2 MR-009 — oxygen/life-support criticality boundary

Run 1 classified MR-009 as Critical.

The policy did not explicitly distinguish a low-output indication from confirmed impairment of actual oxygen delivery to a currently dependent patient.

**Diagnosis:** instruction/policy ambiguity, with an additional register-data-quality issue.

#### 4.3 EQ-009 register information

MR-009 stated that the register listed medium criticality, while the actual register criticality field was blank.

**Diagnosis:** context/data-quality problem.

#### 4.4 Maintenance-report file format

The original maintenance-report source had a `.csv` filename but was actually an Excel Open XML workbook.

**Diagnosis:** workspace/data-format problem.

---

## 5. Run 2 — Targeted Intervention

Run 2 introduced deliberate changes based on the Run 1 diagnosis.

### Workspace/context changes

1. The maintenance-report source was converted to a genuine CSV.
2. `reference/06_data_quality_notes.md` was added.
3. Blank register fields were explicitly defined as unavailable information.
4. Reported register values that could not be verified were defined as unverified claims.

### Policy changes

The policy was clarified in three areas.

#### Stable patient and alternative monitoring

A stable patient with a working alternative monitoring method should be Medium unless there is evidence of immediate safety concern, deterioration, or loss of the only effective monitoring pathway.

The policy also states that if an effective alternative is not established by the report, the agent must not assume one.

#### Oxygen and life-support equipment

Critical requires evidence of actual delivery/support impairment to a currently dependent patient or loss of the only functioning life-support/resuscitation unit.

A low-output or similar indication alone does not establish actual delivery impairment; where impairment is unconfirmed but patient-safety risk is clear, High applies pending confirmation.

#### Verified versus reported register values

Blank register fields remain unavailable.

A value stated by a report but not verifiable in the current register must be treated as a reported claim rather than a verified register value.

### Run 2 observed changes

The deliberate intervention changed the handling of the two Run 1 urgency ambiguities:

- **MR-004:** High → Medium.
- **MR-009:** Critical → High.

The EQ-009 register discrepancy was explicitly identified as unverified rather than inferred.

### New Run 2 issue

MR-010 changed from Biomedical Engineering in Run 1 to Facilities in Run 2.

This was not one of the deliberate Run 2 changes. Investigation showed that the policy contained two textually defensible routing interpretations:

- Biomedical Engineering for physical damage to a device's internal function.
- Facilities for non-patient-connected support equipment such as suction units unless an internal fault was established.

The policy did not clearly define ownership of external/cosmetic damage to a registered portable biomedical asset.

**Diagnosis:** instruction/policy ambiguity.

---

## 6. Run 3 — Routing Ownership Intervention

Run 3 changed only the team-routing boundary exposed by MR-010.

### New routing principle

The first routing question became:

> Is the equipment a registered biomedical equipment asset?

If yes:

> Biomedical Engineering owns it regardless of whether the issue is internal, external/cosmetic, performance-related, or routine maintenance.

If no, the next question is whether it is fixed facility infrastructure.

Fixed infrastructure is routed to Facilities.

If an item is neither a registered biomedical asset nor fixed facility infrastructure, the specialized ICT, Nursing/Supply Chain, or Vendor Escalation rules apply as applicable.

### Reason for the intervention

The previous policy had mixed equipment types with ownership scope. The Run 3 clarification changed the boundary from equipment/fault-type reasoning to asset ownership.

This specifically addressed the distinction between:

- portable, registered medical devices; and
- fixed facility infrastructure.

---

## 7. Run 3 Results

### MR-010 — routing ambiguity resolved

MR-010 became:

- **Urgency:** Low
- **Assigned team:** Biomedical Engineering
- **Confidence:** 0.84

The reasoning states that EQ-010 is a registered biomedical equipment asset, so Biomedical Engineering owns the asset despite external/cosmetic damage, normal suction, and lack of current patient connection.

This demonstrates that the deliberate Run 3 policy change affected agent behavior as intended.

### MR-009 — Run 2 improvement preserved

MR-009 remained:

- High
- Biomedical Engineering

The agent continued to distinguish a low flow-indicator reading from confirmed actual oxygen-delivery impairment and continued to treat the register's claimed medium criticality as unverified.

### Repeated-equipment handling preserved

Run 3 continued to consider:

- EQ-002: MR-002 and MR-013;
- EQ-007: MR-007 and MR-014.

Later reports were considered possible continuations or recurrences where earlier resolution was not documented.

### Missing-information handling preserved

The agent continued to identify specific missing facts rather than inventing them.

Examples included:

- MR-004 — effectiveness of manual spot checks;
- MR-005 — medication identity and interruption duration;
- MR-009 — independent oxygen measurements;
- MR-013 — current problem, status, clinical context, and impact;
- MR-014 — measured charging data and prior-fault resolution.

### Source integrity preserved

Run 3 documented that:

- all 14 reports were processed;
- the irrelevant document was not opened or used;
- the private expected-results file was not accessed;
- prior-run outputs were not used as classification inputs;
- source files were not modified.

---

## 8. Final Comparison of the Iterations

| Issue | Run 1 | Run 2 | Run 3 | Diagnosis |
|---|---|---|---|---|
| MR-004 monitoring urgency | High | Medium | High provisional | Remaining evidence boundary |
| MR-009 oxygen urgency | Critical | High | High | Run 2 clarification preserved |
| EQ-009 register discrepancy | Mixed/unverified | Explicitly flagged | Explicitly flagged | Resolved |
| Report file format | Mislabeled XLSX | Genuine CSV | Genuine CSV | Resolved |
| MR-010 routing | Biomedical Engineering | Facilities | Biomedical Engineering | Run 3 ambiguity resolved |
| MR-003 routing | Vendor-oriented | Vendor-oriented | Biomedical Engineering primary | Consequence of ownership model |
| MR-011 routing | Vendor-oriented | Vendor-oriented | Biomedical Engineering primary | Consequence of ownership model |
| Repeated equipment | Correctly considered | Preserved | Preserved | Stable |
| Missing information | Correctly handled | Preserved | Preserved | Stable |
| Private-file protection | Preserved | Preserved | Preserved | Stable |

---

## 9. Context/Workspace vs. Instructions Diagnosis

### Context/workspace problems

The following were genuine workspace/context problems:

- mislabeled maintenance-report file;
- incomplete register fields;
- lack of explicit data-quality guidance concerning blank and unverified register values.

These were addressed through:

- CSV conversion;
- `06_data_quality_notes.md`;
- explicit policy treatment of unavailable and unverified values.

### Instruction/policy problems

The following were instruction/policy problems:

- stable patient versus effective alternative monitoring;
- oxygen/life-support Critical versus High evidence threshold;
- ownership boundary for registered portable equipment versus fixed infrastructure.

Each was addressed through a targeted policy clarification.

This distinction is important because the experiment did not attempt to solve every failure by simply adding more instructions. Workspace problems were corrected in the workspace; reasoning-boundary problems were addressed in the policy.

---

## 10. Remaining Ambiguity

### MR-004

Run 3 classifies MR-004 as High (provisional) because the patient is stable and manual spot checks are reported, but the report does not establish that manual spot checks constitute an effective alternative monitoring pathway.

This is consistent with the policy's no-assumption rule.

It is therefore documented as a remaining evidence boundary rather than automatically treated as an agent failure.

No further policy iteration was performed solely to force a different result.

---

## 11. Policy Consequence: Laboratory Analyzer Routing

The ownership-based Run 3 model also affects registered laboratory analyzers.

MR-003 and MR-011 are now routed to Biomedical Engineering as the primary asset owner, while vendor service can remain a subsequent technical escalation when manufacturer-level expertise is required.

This is an explicit consequence of the ownership-based routing model and should be understood as such rather than treated as an accidental routing change.

---

## 12. Quality and Safety Controls Demonstrated

The final run demonstrated the following controls:

### Evidence-based classification

The agent distinguished reported observations from confirmed technical causes and did not invent missing facts.

### Uncertainty representation

Confidence scores were provided for every report, with lower scores where classification depended on unresolved information.

### Clinical-context handling

Clinical context could override static register criticality when the policy required it, while unverified register claims were not silently treated as facts.

### Historical context

Repeated equipment IDs were considered so later reports could be evaluated as possible continuations or recurrences.

### Source discipline

The intentionally irrelevant document was excluded, and the private expected-results file remained outside the agent's permitted context.

### Auditability

Each run produced both an output artifact and a trajectory artifact, while each diagnosis documented the reason for the next deliberate intervention.

---

## 13. Evidence Package

The repository contains the following core capstone evidence:

```text
data/
├── 01_maintenance_reports.csv
└── 02_equipment_register.xlsx

reference/
├── 03_maintenance_policy.pdf
├── 04_triage_examples.pdf
├── 05_irrelevant_document.pdf
└── 06_data_quality_notes.md

evidence/
├── run-1/
│   ├── run-1-output.md
│   └── run-1-trajectory.md
├── run-2/
│   ├── run-2-output.md
│   ├── run-2-trajectory.md
│   └── run-2-diagnosis.md
└── run-3/
    ├── run-3-output.md
    ├── run-3-trajectory.md
    └── run-3-diagnosis.md
```

The private expected-results file remains excluded from the public repository.

---

## 14. Final Evaluation

The capstone demonstrated a complete iterative agent-evaluation workflow rather than a single successful prompt execution.

The strongest evidence is the traceable relationship between diagnosis and subsequent behavior:

- Run 1 identified concrete workspace and policy problems.
- Run 2 deliberately corrected those problems.
- Run 2 produced observable changes in MR-004 and MR-009 and exposed a new routing ambiguity.
- Run 3 deliberately corrected the routing ambiguity.
- Run 3 changed MR-010 to Biomedical Engineering while preserving the major Run 2 improvements.
- The remaining MR-004 boundary was explicitly identified rather than hidden or repeatedly optimized away.

The final state therefore provides a reviewable record of:

> **task design → context/workspace setup → baseline run → diagnosis → deliberate iteration → second run → diagnosis → targeted routing iteration → third run → final evaluation**

No additional run was performed because further iteration would primarily risk optimizing individual classifications rather than addressing a demonstrated general workspace or policy defect.

---

## 15. Reviewer-Facing Takeaway

This project demonstrates that the agent was not evaluated solely on whether its first answer matched an expected answer.

Instead, the evaluation focused on whether:

1. the task was professionally meaningful and multi-step;
2. the workspace contained controlled, reviewable context;
3. instructions and policy defined an explicit quality standard;
4. the agent produced auditable artifacts;
5. failures could be diagnosed as context/workspace or instruction problems;
6. interventions were deliberate and traceable;
7. subsequent runs produced observable behavioral changes;
8. successful behaviors were preserved across iterations;
9. private evaluation material remained protected.

The three-run trajectory provides the evidence for that process.
