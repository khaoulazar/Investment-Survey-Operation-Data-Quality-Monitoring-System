## Phase 4 — ETL et contrôle qualité des données

Cette phase agit comme une chaîne de contrôle qualité en usine : chaque vue 
inspecte un aspect différent des données avant qu'elles soient jugées fiables 
pour l'analyse.

### EX-13 — Vue_Coherence_Imputation_Validee
Vérifie la cohérence entre la méthode d'imputation et le statut de non-réponse 
dans Fait_Qualite. Correction appliquée via UPDATE.

### EX-14 — Vue_Completude_Validee
Vérifie la complétude des données par unité enquêtée.

### EX-15 — Vue_Couverture_Validee
Détecte les unités enquêtées sans suivi terrain associé (LEFT JOIN + 
IS NULL). Résultat : 0 anomalie — couverture terrain complète (2 068/2 068 
unités).

### EX-16 — Vue_Plages_Valeurs_Validee
Vérifie que les indicateurs numériques (écart GPS, nombre de tentatives, 
corrections, erreurs CAPI) restent dans des plages plausibles :
- écart_gps_metres : négatif → NULL (erreur de calcul en amont) ; > 700m → 
  signalé
- nb_tentatives > 10 → signalé
- nb_corrections > 10 → signalé
- nb_erreurs_capi > 10 → signalé

Toutes les anomalies détectées sur une même unité sont cumulées dans un seul 
champ `anomalie`. Résultat : 0 anomalie détectée.

### EX-17 — Vue_Coherence_Dates_Validee
Vérifie que les dates de Fait_Suivi_Terrain et Fait_Qualite tombent dans la 
période de collecte (01/01/2025–01/07/2025) et correspondent entre elles pour 
une même unité. Résultat : 0 anomalie détectée.

**Bilan Phase 4 :** 5 vues de contrôle qualité créées et validées. Aucune 
anomalie structurelle détectée sur les données générées, confirmant la 
robustesse du script `generate_data.py`.
