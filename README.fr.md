🇬🇧 [English](README.md) | 🇫🇷 **Français**

---

# Système de suivi et de contrôle qualité de la collecte des données de l'enquête sur l'investissement public
## Stack technique

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![SQL](https://img.shields.io/badge/SQLite-07405E?style=for-the-badge&logo=sqlite&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white)
![VS Code](https://img.shields.io/badge/VS%20Code-007ACC?style=for-the-badge&logo=visualstudiocode&logoColor=white)

Système de suivi et de contrôle qualité de bout en bout pour une enquête administrative portant sur 2 068 unités — conception d'un schéma en étoile, détection de valeurs aberrantes et tableau de bord Power BI.

## Présentation générale du projet

Ce projet consiste à concevoir et documenter un **système de suivi et de contrôle qualité d'une enquête administrative**, sur la base de données anonymisées, mais avec une méthodologie directement inspirée des pratiques professionnelles d'enquête statistique (suivi de terrain, contrôle de cohérence, indices de qualité). Le cas d'usage retenu est une enquête exhaustive sur les investissements des administrations publiques marocaines, couvrant 2 068 unités administratives (collectivités territoriales, établissements publics administratifs, sociétés de développement local) réparties sur 12 régions, mobilisant 48 enquêteurs et 12 superviseurs régionaux sur une période de terrain de 7 mois. L'objectif n'est pas de produire un simple tableau de bord, mais de reconstituer, de bout en bout, le rôle qu'occuperait un analyste de données chargé de concevoir ce système : du cadrage des besoins jusqu'à sa gouvernance documentaire, en passant par la modélisation des données, le traitement de la qualité statistique et la restitution décisionnelle.

La démarche est structurée en huit phases successives, chacune produisant un livrable concret qui conditionne les phases suivantes:
<br>**1-Cadrage:** traduit les compétences à démontrer en exigences fonctionnelles précises et traçables.
<br>**2-Modélisation des données:** construit, à partir de ces exigences, un schéma en étoile normalisé compatible SQLite et Azure SQL Database.
<br>**3-Architecture technique:** définit l'infrastructure de collecte, de stockage et d'ingestion des données. 
<br>**4-ETL et contrôle qualité:** met en œuvre les règles de validation, la détection des valeurs aberrantes par IQR et Z-score, et le calcul de l'indice composite de qualité.
<br>**5-Indicateurs de pilotage:** formalise les KPIs de couverture, de vélocité et de qualité. 
<br>**6-Visualisation Power BI:** restitue l'ensemble sous forme de tableau de bord interactif à trois niveaux de lecture.
<br>**7-Automatisation et alertes:** introduit des mécanismes de notification et un score de risque par unité. 
<br>**8-Documentation et gouvernance:** consolide le dictionnaire de données, le journal des décisions méthodologiques et la documentation technique du projet.
