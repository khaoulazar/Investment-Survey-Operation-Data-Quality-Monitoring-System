# Phase 3 — Technical Architecture and Infrastructure

## 3.1 Objective

Define the technical environment on which the Phase 2 schema will be implemented: database engine, simulated-data generation tooling, and analysis chain — while ensuring real portability to Azure SQL Database (REQ-01, REQ-12), not just a declared one.

## 3.2 Components

| Component | Tool selected | Justification |
|---|---|---|
| Database (portfolio) | SQLite | No server installation required, versionable `.db` file in the repo, sufficient for 2,068 rows |
| Target compatibility | Azure SQL Database | Required by REQ-12 — implies avoiding SQLite-only functions in queries intended for reuse |
| Simulated data generation | Python (`faker`, `random`, `numpy`) | Fine control over distributions and anomaly/non-response rates |
| Loading data into SQLite | Python (native `sqlite3`) | A `generate_data.py` script populates the 9 tables in dependency order (dimensions before facts) |
| Analysis (IQR/Z-score, composite index) | SQL + Python (`pandas`, `scipy`) | SQL for simple aggregates, Python for statistical calculations (REQ-04, REQ-07) |
| Reporting | Power BI Desktop | Direct connection to the SQLite file (or intermediate CSV export) |

## 3.3 Technical decisions

| Decision | Choice made | Rationale |
|---|---|---|
| Data generation order | Dimensions first (supervisor → region → unit type → surveyor → surveyed unit → calendar), then facts | Required to satisfy FK constraints |
| Enforcing 1:1 at generation time | Exactly one row per `unite_id` in `Fait_Suivi_Terrain` and `Fait_Qualite` | Consistent with the UNIQUE constraint set in Phase 2 |
| Surveyor ↔ region anchoring at generation time | Each surveyor is attached to a single region at creation; units only draw their agent from surveyors of their own region | Prevents a surveyor from ending up with units across multiple regions |
| Per-region headcount source | Real EIAP table (human and material resource needs), hardcoded in `REGIONS_DATA` | Replaces a uniform distribution with exact per-region figures (e.g. Marrakech-Safi: 299 units/7 surveyors/7 vehicles/8 tablets; Eddakhla-Oued Eddahab: 24 units/1 surveyor/1 vehicle/1 tablet) |
| Number of supervisors | 12 total (1 per region, strict 1:1) | Each supervisor is assigned to the region where they reside |
| Simulated anomaly rate | **~10–15%** of units with an aberrant `ecart_gps_metres` or a high `nb_erreurs_capi` | Moderate: enough to demonstrate IQR/Z-score detection (REQ-04) without overly distorting overall KPIs |
| Simulated non-response rate | **~10%** of units with `non_reponse` = Yes | Realistic for a mandatory administrative survey, without dominating the indicators |
| Reproducibility | `random.seed(42)` fixed in the script | Ensures every run produces exactly the same data, for comparable analyses |
| Database file location | `data/enquete_administrative.db` at the repo root | Consistent with the GitHub structure already in place |
| Data-generation script location | `scripts/generate_data.py` | Clear separation between code and data |

## 3.4 End-of-phase deliverable

Technical specification of the environment (SQLite + Python + Power BI), real per-region headcounts integrated (EIAP table), confirmed simulation parameters (10–15% anomalies, 10% non-response), confirmed repo folder structure (`data/`, `scripts/`), functional and validated `generate_data.py` script (0 surveyor/region inconsistencies).
