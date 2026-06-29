# Phase 2 — Modélisation des données

## 2.1 Objectif de la phase

Formaliser, à partir des exigences et décisions actées en Phase 1, un schéma de données complet et contraint : tables, types, clés primaires, clés étrangères, et cardinalités réelles. Le schéma est  directement traduisible en DDL SQL (Phase 3), sans colonne manquante ni relation mal définie.

## 2.2 Principe structurant

Schéma en étoile : `Dim_Unite_Enquetee` est la **table de filtre centrale**, entourée de quatre dimensions descriptives (`Dim_Region`, `Dim_Type_Unite`, `Dim_Enqueteur`, `Dim_Calendrier`) et de deux tables de faits en relation **strictement 1:1** avec elle (`Fait_Suivi_Terrain`, `Fait_Qualite`) — chacune représentant l'état courant de suivi et de qualité d'une unité, pas un historique d'événements. Une septième dimension (`Dim_Superviseur`) encadre `Dim_Region`, et une table de faits annexe (`Fait_Allocation_Ressources`) rattache les ressources logistiques à la région.

![Schéma en étoile](images/star-schema.png)

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
- `region_id` (FK → Dim_Region) — *ajouté : chaque enquêteur est ancré à une seule région, ce qui empêche de lui assigner des unités d'une autre région*
- `anciennete_annees`
- `niveau_experience`
- `niveau_competence_it`

**Dim_Unite_Enquetee** *(table de filtre centrale)*
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

## 2.4 Faits

**Fait_Allocation_Ressources**
- `allocation_id` (PK)
- `region_id` (FK → Dim_Region)
- `nb_vehicules`
- `nb_tablettes`
- `budget_carburant_dh`

**Fait_Suivi_Terrain** — relation **1:1** avec `Dim_Unite_Enquetee`
- `suivi_id` (PK)
- `unite_id` (FK → Dim_Unite_Enquetee, **UNIQUE**)
- `date_id` (FK → Dim_Calendrier) — *ajouté en Phase 1, requis pour EX-06*
- `mode_collecte`
- `nb_tentatives`
- `ecart_gps_metres`
- `etat_questionnaire`

**Fait_Qualite** — relation **1:1** avec `Dim_Unite_Enquetee`
- `qualite_id` (PK)
- `unite_id` (FK → Dim_Unite_Enquetee, **UNIQUE**)
- `date_id` (FK → Dim_Calendrier) — *ajouté en Phase 1, requis pour EX-06*
- `nb_corrections`
- `nb_erreurs_capi`
- `methode_imputation`
- `non_reponse`
- `reinterview_requise`

## 2.5 Cardinalités actées

| Relation | Cardinalité | Mécanisme de contrainte |
|---|---|---|
| Dim_Superviseur → Dim_Region | 1 — 1 | `UNIQUE(superviseur_id)` sur `Dim_Region` |
| Dim_Region → Dim_Enqueteur | 1 — * | FK simple (chaque enquêteur ancré à une seule région) |
| Dim_Region → Dim_Unite_Enquetee | 1 — * | FK simple |
| Dim_Type_Unite → Dim_Unite_Enquetee | 1 — * | FK simple |
| Dim_Enqueteur → Dim_Unite_Enquetee | 1 — * | FK simple |
| Dim_Region → Fait_Allocation_Ressources | 1 — * | FK simple |
| Dim_Unite_Enquetee → Fait_Suivi_Terrain | 1 — 1 | `UNIQUE(unite_id)` sur `Fait_Suivi_Terrain` |
| Dim_Unite_Enquetee → Fait_Qualite | 1 — 1 | `UNIQUE(unite_id)` sur `Fait_Qualite` |
| Dim_Calendrier → Fait_Suivi_Terrain | 1 — * | FK simple |
| Dim_Calendrier → Fait_Qualite | 1 — * | FK simple |

## 2.6 Décisions de modélisation actées

| Décision | Choix retenu | Raison |
|---|---|---|
| Cardinalité Unité ↔ Suivi terrain / Qualité | 1:1, imposée par `UNIQUE(unite_id)` | Une unité = un état de suivi courant et un indicateur qualité courant, pas un historique |
| Cardinalité Superviseur ↔ Région | 1:1, imposée par `UNIQUE(superviseur_id)` | Un superviseur ne couvre qu'une seule région (confirmé) |
| Ancrage enquêteur ↔ région | `region_id` ajouté sur `Dim_Enqueteur` | Empêche qu'un enquêteur soit assigné à des unités de plusieurs régions différentes |
| Effectifs réels par région | Chiffres réels (tableau EIAP) : unités, enquêteurs, véhicules, tablettes varient par région (ex. Marrakech-Safi : 299 unités / 7 enquêteurs ; Eddakhla-Oued Eddahab : 24 unités / 1 enquêteur) | Remplace l'hypothèse initiale de répartition uniforme |
| Superviseurs | 12 au total (1 par région, strict 1:1) | Décision actée malgré le tableau source qui n'en compte que 9 (3 régions sans superviseur réel) |
| Pondération | Aucune colonne de poids de sondage | Recensement exhaustif confirmé en Phase 1 |
| Lien temporel sur les faits | `date_id` ajouté sur `Fait_Suivi_Terrain` et `Fait_Qualite` | Nécessaire pour EX-06 (tendance dans le temps), absent du schéma initial |

## 2.7 Livrable de fin de phase

Schéma de données complet (9 tables), cardinalités précisées, justifiées et contraintes (UNIQUE où le 1:1 est requis ; ancrage enquêteur-région assuré par FK), prêt pour traduction en DDL SQL — voir le diagramme en étoile corrigé ci-dessus.
