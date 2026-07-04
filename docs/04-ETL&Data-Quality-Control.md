## Phase 4 — ETL and Data Quality Control

This phase works like a quality control line in a factory: each view 
inspects a different aspect of the data before it can be trusted for 
analysis.

### REQ-13 — Vue_Coherence_Imputation_Validee
Checks consistency between imputation method and non-response status in 
Fait_Qualite. Correction applied via UPDATE.

### REQ-14 — Vue_Completude_Validee
Checks data completeness per surveyed unit.

### REQ-15 — Vue_Couverture_Validee
Detects surveyed units with no associated field follow-up (LEFT JOIN + 
IS NULL). Result: 0 anomalies — full field coverage (2,068/2,068 units).

### REQ-16 — Vue_Plages_Valeurs_Validee
Checks that numeric indicators (GPS deviation, number of attempts, 
corrections, CAPI errors) stay within plausible ranges:
- ecart_gps_metres: negative → NULL (upstream calculation error); > 700m → 
  flagged
- nb_tentatives > 10 → flagged
- nb_corrections > 10 → flagged
- nb_erreurs_capi > 10 → flagged

All anomalies detected for a given unit are cumulated into a single 
`anomalie` field. Result: 0 anomalies detected.

### REQ-17 — Vue_Coherence_Dates_Validee
Checks that dates in Fait_Suivi_Terrain and Fait_Qualite fall within the 
survey period (2025-01-01–2025-07-01) and match each other for the same 
unit. Result: 0 anomalies detected.

**Phase 4 summary:** 5 data quality views created and validated. No 
structural anomalies detected in the generated data, confirming the 
robustness of the `generate_data.py` script.
