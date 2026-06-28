# Phase 2 — Data Modeling

## 2.1 Objective

Based on the requirements and decisions made in Phase 1, this phase formalizes a complete, constrained data schema: tables, types, primary keys, foreign keys, and real cardinalities — with no ambiguity. The schema must be directly translatable into SQL DDL (Phase 3), with no missing column or poorly defined relationship.

## 2.2 Structuring principle

Star schema: `Dim_Unite_Enquetee` is the **central filter table**, surrounded by four descriptive dimensions (`Dim_Region`, `Dim_Type_Unite`, `Dim_Enqueteur`, `Dim_Calendrier`) and two fact tables in a **strict 1:1 relationship** with it (`Fait_Suivi_Terrain`, `Fait_Qualite`) — each representing the current monitoring/quality state of a unit, not an event history. A seventh dimension (`Dim_Superviseur`) frames `Dim_Region`, and an additional fact table (`Fait_Allocation_Ressources`) links logistical resources to the region.

![Star schema](images/star-schema.png)

## 2.3 Dimensions

**Dim_Superviseur**
- `superviseur_id` (PK)
- `email`, `telephone`
- `date_embauche`
- `anciennete_annees`

**Dim_Region**
- `region_id` (PK)
- `zone_geographique`
- `population`
- `superficie_km2`
- `superviseur_id` (FK → Dim_Superviseur, **UNIQUE**)

**Dim_Type_Unite**
- `type_unite_id` (PK)
- `description`
- `priorite_collecte`

**Dim_Enqueteur**
- `agent_id` (PK)
- `anciennete_annees`
- `niveau_experience`
- `niveau_competence_it`

**Dim_Unite_Enquetee** *(central filter table)*
- `unite_id` (PK)
- `region_id` (FK → Dim_Region)
- `type_unite_id` (FK → Dim_Type_Unite)
- `agent_id` (FK → Dim_Enqueteur)
- `secteur_activite`
- `milieu`

**Dim_Calendrier**
- `date_id` (PK)
- `trimestre`
- `est_jour_ouvrable`

## 2.4 Facts

**Fait_Allocation_Ressources**
- `allocation_id` (PK)
- `region_id` (FK → Dim_Region)
- `nb_vehicules`
- `nb_tablettes`
- `budget_carburant_dh`

**Fait_Suivi_Terrain** — **1:1** relationship with `Dim_Unite_Enquetee`
- `suivi_id` (PK)
- `unite_id` (FK → Dim_Unite_Enquetee, **UNIQUE**)
- `date_id` (FK → Dim_Calendrier) — *added in Phase 1, required for REQ-06*
- `mode_collecte`
- `nb_tentatives`
- `ecart_gps_metres`
- `etat_questionnaire`

**Fait_Qualite** — **1:1** relationship with `Dim_Unite_Enquetee`
- `qualite_id` (PK)
- `unite_id` (FK → Dim_Unite_Enquetee, **UNIQUE**)
- `date_id` (FK → Dim_Calendrier) — *added in Phase 1, required for REQ-06*
- `nb_corrections`
- `nb_erreurs_capi`
- `methode_imputation`
- `non_reponse`
- `reinterview_requise`

## 2.5 Confirmed cardinalities

| Relationship | Cardinality | Constraint mechanism |
|---|---|---|
| Dim_Superviseur → Dim_Region | 1 — 1 | `UNIQUE(superviseur_id)` on `Dim_Region` |
| Dim_Region → Dim_Unite_Enquetee | 1 — * | Simple FK |
| Dim_Type_Unite → Dim_Unite_Enquetee | 1 — * | Simple FK |
| Dim_Enqueteur → Dim_Unite_Enquetee | 1 — * | Simple FK |
| Dim_Region → Fait_Allocation_Ressources | 1 — * | Simple FK |
| Dim_Unite_Enquetee → Fait_Suivi_Terrain | 1 — 1 | `UNIQUE(unite_id)` on `Fait_Suivi_Terrain` |
| Dim_Unite_Enquetee → Fait_Qualite | 1 — 1 | `UNIQUE(unite_id)` on `Fait_Qualite` |
| Dim_Calendrier → Fait_Suivi_Terrain | 1 — * | Simple FK |
| Dim_Calendrier → Fait_Qualite | 1 — * | Simple FK |

## 2.6 Modeling decisions

| Decision | Choice made | Rationale |
|---|---|---|
| Unit ↔ Field monitoring / Quality cardinality | 1:1, enforced via `UNIQUE(unite_id)` | A unit has one current monitoring state and one current quality indicator, not a history |
| Supervisor ↔ Region cardinality | 1:1, enforced via `UNIQUE(superviseur_id)` | A supervisor covers exactly one region (confirmed) |
| Weighting | No sampling-weight column | Exhaustive census confirmed in Phase 1 |
| Time link on fact tables | `date_id` added to `Fait_Suivi_Terrain` and `Fait_Qualite` | Required for REQ-06 (trends over time), missing from the initial schema |

## 2.7 End-of-phase deliverable

Complete data schema (9 tables), with cardinalities specified, justified, and constrained (UNIQUE where 1:1 is required), ready for translation into SQL DDL — see the corrected star schema diagram above.
