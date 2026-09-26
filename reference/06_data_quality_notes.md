# Data Quality Notes

1. The maintenance report dataset contains 14 reports.
2. Each report's equipment_id should be cross-referenced with the equipment register.
3. The equipment register contains equipment IDs and equipment types.
4. Several register fields are blank, including department, manufacturer,
   model, criticality, assigned team, maintenance frequency,
   last maintenance date, and operational status.
5. Blank register fields must be treated as unavailable information.
6. Do not infer values for blank register fields.
7. Where a report itself references an unverified register value,
   distinguish the reported statement from a verified register value.
