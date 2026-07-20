## Méthodologie d'extraction des métadonnées

Le schéma des tables , colonnes , mesures et relations a été extrait directement du modèle Power BI via les
fonctions DAX `INFO.VIEW.TABLES()`, `INFO.VIEW.COLUMNS()` et
`INFO.VIEW.RELATIONSHIPS()`, garantissant une documentation fidèle à la
structure réelle du modèle (plutôt que documentée manuellement, donc
sujette à désynchronisation). 
INFO.VIEW regroupe une série de fonctions DAX simplifiées pour interroger les métadonnées du modèle directement dans une requête ou une mesure.
Elle est parmi les nouveautés du PowerBI en juin 2026.
