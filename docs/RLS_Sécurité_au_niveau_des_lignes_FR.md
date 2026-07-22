# RLS: Une solution évolutive et sécurisée

## Sécurité au niveau des lignes (RLS) — Contrôle d'accès régional aux données

## Aperçu

Ce tableau de bord est partagé entre **12 régions**, chacune avec son propre superviseur et ses équipes de terrain, mais il repose sur un **modèle Power BI unique**, et non 12 rapports distincts. La sécurité au niveau des lignes (Row-Level Security, RLS) est le mécanisme qui rend cela possible : elle filtre dynamiquement les données visibles par chaque utilisateur selon son identité, afin que chaque superviseur ouvre le même tableau de bord tout en ne voyant que sa propre région.

## Pourquoi la RLS — la justification métier

Avant la mise en place de la RLS, les alternatives étaient :
- **Un tableau de bord par région** — 12 rapports à construire, maintenir et synchroniser à chaque modification d'une mesure ou d'un visuel
- **Un tableau de bord partagé sans restriction** — chaque superviseur pouvait voir les données de toutes les autres régions, ce qui pose à la fois un risque de gouvernance et un problème de confiance pour une opération de collecte de données impliquant 48 enquêteurs et des données sensibles sur les unités administratives

La RLS résout les deux problèmes à la fois : **un seul modèle, un seul ensemble de visuels, un seul endroit où maintenir la logique** — avec des limites d'accès appliquées au niveau de la donnée elle-même, et non laissées à la convention ou à la discipline de chaque superviseur.

### Ce que la RLS apporte

- **Zéro fuite de données entre régions.** Un superviseur de la région A ne peut ni voir, ni filtrer, ni exporter les données de la région B — non pas grâce à un simple filtre masqué, mais parce que la requête sous-jacente elle-même est délimitée avant même d'atteindre la couche visuelle.
- **Un seul tableau de bord, 12 superviseurs, plusieurs équipes d'agents.** Un rapport publié unique dessert simultanément les 12 régions et leurs équipes d'enquêteurs. Aucune maintenance dupliquée, aucune divergence de version entre copies régionales.
- **~80 % d'amélioration des temps de chargement.** Les requêtes de chaque utilisateur sont limitées aux lignes de sa propre région plutôt que de parcourir les 2 068 unités administratives. Filtrer à la source, plutôt qu'après coup, est ce qui améliore réellement la performance — et cela rappelle que l'amélioration la plus utile d'un tableau de bord n'est presque jamais un nouveau graphique. C'est le contrôle de **qui voit quoi, et combien de données chacun doit parcourir pour le voir.**

## Fonctionnement

1. **Une table de correspondance de sécurité** associe chaque utilisateur Power BI (par email/UPN) à la ou les régions qu'il est autorisé à consulter.
2. **Une expression de filtre DAX au niveau du rôle** applique cette correspondance aux tables de faits et de dimensions, afin que chaque visuel, mesure et niveau de détail respecte automatiquement la limite — sans configuration visuel par visuel.
3. **Les rôles sont attribués dans le service Power BI** sous **Sécurité → Row-level security**, en associant le compte de chaque superviseur au rôle de sa région.
4. **La propagation du filtre** suit les relations du modèle : restreindre la dimension région se répercute automatiquement sur les tables de faits et tous les visuels dépendants, sans dupliquer la logique de filtrage ailleurs.

## Tests et validation

Avant publication, chaque rôle a été validé à l'aide de la fonction **Afficher en tant que rôles** dans Power BI Desktop, en confirmant pour chacune des 12 régions que :
- Seules les unités administratives, les enquêteurs et les indicateurs d'avancement de cette région sont visibles
- Les totaux agrégés (cartes KPI, diagrammes de Pareto globaux) reflètent correctement le sous-ensemble délimité, et non l'ensemble des données
- Aucune exploration croisée entre régions ni info-bulle ne laisse fuiter des données hors du périmètre attribué

## À retenir

La sécurité n'est pas une fonctionnalité ajoutée après coup à un tableau de bord terminé — elle fait partie de la conception du modèle de données dès le départ. Pour un outil opérationnel partagé entre plusieurs équipes régionales, **la RLS a transformé une exigence de gouvernance en gain de performance.**
