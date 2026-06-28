# Phase 1 — Requirements Gathering

## 1.1 Objective

Before any data modeling work, this phase translates the skills to be demonstrated into precise, traceable functional requirements, mapped to the tables and columns that will serve them. No element of the data schema (Phase 2) will be retained unless it answers a requirement stated here. This discipline ensures a lean, justifiable model directly aligned with the targeted deliverables.

## 1.2 Project scope

| Parameter | Value |
|---|---|
| Subject | Monitoring and quality-control system for an administrative survey |
| Nature | Personal project — methodology inspired by professional survey statistics practices |
| Data | Anonymized / simulated |
| Population covered | 2,068 administrative units (CT / EPA / SDL), 12 regions |
| Survey type | Exhaustive census — no statistical weighting |
| Reference period | 7 months (01/01/2025 → 01/07/2025) |

## 1.3 Functional requirements

| Ref. | Requirement | Justification |
|---|---|---|
| REQ-01 | Design a normalized star schema, compatible with SQLite and Azure SQL | Data modeling |
| REQ-02 | Calculate response rate by region | SQL & statistical analysis |
| REQ-03 | Calculate imputation rate by region | SQL & statistical analysis |
| REQ-04 | Detect outliers using two distinct methods (IQR and Z-score) | SQL & statistical analysis |
| REQ-05 | Rank regions by data quality | SQL & statistical analysis |
| REQ-06 | Track response rate trends over time | SQL & statistical analysis |
| REQ-07 | Build a composite quality index (response rate, delay rate, anomaly rate) | SQL & statistical analysis |
| REQ-08 | Deliver an interactive regional map | Power BI dashboard |
| REQ-09 | Display response and imputation rate KPIs | Power BI dashboard |
| REQ-10 | Generate conditional visual alerts for at-risk regions | Power BI dashboard |
| REQ-11 | Track time-series trends using DAX time intelligence | Power BI dashboard |
| REQ-12 | Ensure architecture is portable to Azure SQL Database | Data modeling |

## 1.4 Traceability matrix

| Ref. | Required data | Source | Calculation |
|---|---|---|---|
| REQ-02 | Units with `non_reponse` = No / total units, by region | `Fait_Qualite` ⨝ `Dim_Unite_Enquetee` ⨝ `Dim_Region` | SQL aggregate |
| REQ-03 | Units with `methode_imputation` populated / total units that responded, by region | `Fait_Qualite.methode_imputation` | SQL aggregate |
| REQ-04 | IQR and Z-score thresholds on `ecart_gps_metres` (Fait_Suivi_Terrain) and `nb_erreurs_capi` (Fait_Qualite), by region × unit-type stratum | `Fait_Suivi_Terrain`, `Fait_Qualite` | SQL + Python (scipy) |
| REQ-05 | Region rank on composite index | SQL view on composite index | `RANK()` |
| REQ-06 | Indicator trends by quarter | `Fait_Suivi_Terrain.date_id` / `Fait_Qualite.date_id` ⨝ `Dim_Calendrier` | SQL/DAX time series |
| REQ-07 | Index = f(response rate, delay rate, anomaly rate) — delay rate = % units with `nb_tentatives` ≥ 3 | `Fait_Qualite`, `Fait_Suivi_Terrain` | Weighted formula (Phase 4) |
| REQ-08–11 | Reporting of the above indicators | Power BI on SQLite/Azure SQL | DAX + Power Query |

## 1.5 Scoping decisions

| Decision | Choice made | Rationale |
|---|---|---|
| Reference schema | Validated real schema (Dim_Superviseur, Dim_Region, Dim_Type_Unite, Dim_Enqueteur, Dim_Unite_Enquetee, Dim_Calendrier, Fait_Allocation_Ressources, Fait_Suivi_Terrain, Fait_Qualite) | Replaces initial assumptions (`Fait_Saisie` does not exist) |
| Time link on fact tables | Added a `date_id` foreign key on `Fait_Suivi_Terrain` and `Fait_Qualite` | Required for REQ-06 (trends over time), missing from the initial schema |
| Anomaly-detection variable | `ecart_gps_metres` **and** `nb_erreurs_capi`, in parallel | No monetary amount variable available — the system monitors operational quality, not investment data |
| Outlier methods | IQR **and** Z-score, in parallel, by region × unit-type stratum | Explicit requirement REQ-04 |
| Delay-rate definition | % of units with `nb_tentatives` ≥ 3 | No assignment/completion date pair available for a true delay calculation; threshold adjustable in Phase 4 |
| Composite index | 3 components: response, delay (attempts proxy), anomaly | Strictly aligned with REQ-07 |
| Working-day calendar | Weekends excluded only (simplified version) | Future refinement possible: exclude Moroccan public holidays |

## 1.6 End-of-phase deliverable

A numbered requirements register (REQ-01 to REQ-12) and a requirement → data → source traceability matrix, serving as the control reference for Phase 2.
