# Phase 1 — Cadrage et recueil des besoins

## 1.1 Objectif de la phase

Avant toute modélisation de données, cette phase vise à traduire les compétences à démontrer en exigences fonctionnelles précises et traçables jusqu'aux tables et colonnes qui les serviront. Aucun élément du schéma de données (Phase 2) c'est retenu s'il ne répond pas à une exigence formulée ici.

## 1.2 Cadre du projet

| Paramètre | Valeur |
|---|---|
| Objet | Système de suivi et de contrôle qualité d'une enquête administrative |
| Nature | Méthodologie inspirée de pratiques professionnelles d'enquête statistique |
| Données | Anonymisées 
| Population couverte | 2 068 unités administratives (CT / EPA / SDL), 12 régions |
| Type d'opération | Recensement exhaustif — aucune pondération statistique |
| Durée de référence | 7 mois (01/01/2025 → 01/07/2025) |

## 1.3 Exigences fonctionnelles

| Réf. | Exigence | Justification |
|---|---|---|
| EX-01 | Modéliser un schéma en étoile normalisé, compatible SQLite et Azure SQL | Data modeling |
| EX-02 | Calculer le taux de réponse par région | SQL & analyse statistique |
| EX-03 | Calculer le taux d'imputation par région | SQL & analyse statistique |
| EX-04 | Détecter les valeurs aberrantes selon deux méthodes distinctes (IQR et Z-score) | SQL & analyse statistique |
| EX-05 | Classer (ranking) les régions selon la qualité des données | SQL & analyse statistique |
| EX-06 | Suivre l'évolution du taux de réponse dans le temps | SQL & analyse statistique |
| EX-07 | Construire un indice composite de qualité (taux de réponse, taux de retard, taux d'anomalie) | SQL & analyse statistique |
| EX-08 | Restituer une carte régionale interactive | Dashboard Power BI |
| EX-09 | Afficher des KPIs de taux de réponse et d'imputation | Dashboard Power BI |
| EX-10 | Générer des alertes visuelles conditionnelles pour les régions à risque | Dashboard Power BI |
| EX-11 | Suivre les tendances temporelles via l'intelligence temporelle DAX | Dashboard Power BI |
| EX-12 | Garantir une architecture portable vers Azure SQL Database | Data modeling |

## 1.4 Matrice de traçabilité

| Réf. | Donnée requise | Source | Calcul |
|---|---|---|---|
| EX-02 | Unités ayant répondu (`reponse` = Oui) / total unités, par région | `Fait_Saisie` ⨝ `Dim_Unite_Enquetee` ⨝ `Dim_Région` | Agrégat SQL |
| EX-03 | Unités corrigées / total ayant répondu, par région | `Fait_Qualité` ⨝ `Fait_Saisie` ⨝ `Dim_Région` | Agrégat SQL |
| EX-04 | Bornes IQR et Z-score par strate région × type | `Fait_Qualité.methode_detection` | SQL + Python (scipy) |
| EX-05 | Rang de chaque région sur l'indice composite | Vue SQL sur l'indice composite | `RANK()` |
| EX-06 | Taux de réponse par semaine/mois | `Fait_Saisie.date_saisie` ⨝ `Dim_Calendrier` | Série temporelle SQL/DAX |
| EX-07 | Indice = f(réponse, retard, anomalie) | `Fait_Saisie`, `Fait_Suivi_Terrain`, `Fait_Qualité` | Formule pondérée (Phase 4) |
| EX-08–11 | Restitution des indicateurs ci-dessus | Power BI sur SQLite/Azure SQL | DAX + Power Query |

## 1.5 Décisions de cadrage actées

| Décision | Choix retenu | Raison |
|---|---|---|
| Granularité du suivi terrain | Compteur `nbre_tentatives` par unité | Aucune exigence ne demande l'historique détaillé par visite |
| Simulation temporelle | Instantané figé à J+100 (`CURRENT_DAY_OFFSET`) | Uniquement pour la démonstration portfolio |
| Calendrier ouvrable | Week-ends exclus uniquement (v1) | Raffinement (jours fériés) reporté |
| Méthodes d'outliers | IQR **et** Z-score, en parallèle | Exigence EX-04 explicite |
| Indice composite | 3 composantes : réponse, retard, anomalie | Aligné strictement sur EX-07 |
| Pondération de l'indice | À trancher en Phase 4 | Décision différée, assumée |

## 1.6 Livrable de fin de phase

Référentiel d'exigences numérotées (EX-01 à EX-12) et matrice de traçabilité exigence → donnée → source, servant de référence de contrôle pour la Phase 2.
