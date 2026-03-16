# Roadmap référentiel

Voici la roadmap complète pour couvrir l'intégralité du référentiel.

## Bloc 1 : Piloter la conduite d’un projet data

Ce bloc valide la posture de "Lead" ou "Chef de Projet".

    C1 (Analyse du besoin) : Rédiger un compte-rendu d'entretien (fictif ou réel) avec un "Expert Énergie" de la région pour définir les indicateurs clés (ex: taux d'autoconsommation).

    C2 (Cartographie) : Créer un schéma (format Draw.io) listant les sources : API Enedis, API Open Data Réseaux Énergies (ODRE) et fichiers météo historiques.

    C3 (Cadre technique) : Rédiger un chapitre comparatif justifiant le choix de SeaweedFS/ClickHouse face à une architecture Cloud (coût, souveraineté des données).

    C4 (Veille) : Ajouter une annexe "Veille technologique" sur l'évolution du format Parquet ou sur les nouvelles régulations RGPD concernant les données Linky.

    C5 (Planification) : Produire un diagramme de Gantt (ou une Roadmap) montrant les phases d'ingestion, de stockage et de visualisation.

    C6 (Supervision) : Utiliser un outil de ticketing (type GitHub Issues ou Trello) pour montrer comment gérer les bugs dans le code.

    C7 (Communication) : Préparer un support de présentation pour une "réunion de lancement" (Kick-off) destinée aux parties prenantes.

## Bloc 2 : Collecte, stockage et mise à disposition

Ici, on prouve que la donnée circule proprement.

    C8 (Réalisation collecte) : Développer le script Python (utilisant requests et boto3) qui automatise la récupération mensuelle des données Enedis.

    C9 (Requêtes SQL) : Écrire des scripts SQL complexes dans ClickHouse pour extraire des tendances (ex: moyenne mobile de conso sur 12 mois).

    C10 (Règles d'agrégation) : Implémenter un script de nettoyage qui gère les doublons ou les erreurs de capteurs (ex: remplacer une valeur aberrante par la moyenne du quartier).

    C11 (Création BDD) : Configurer l'instance ClickHouse avec des politiques de rétention de données.

    C12 (Partage du jeu de données) : Créer une vue SQL ou une API simple (FastAPI) pour permettre à un Data Scientist de consommer les données propres.

## Bloc 3 : Élaborer et maintenir un entrepôt de données (DWH)

On se concentre sur ClickHouse en tant que structure organisée.

    C13 (Modélisation) : Schématiser le modèle de données (Schéma en étoile ou table large MergeTree).

    C14 (Création DWH) : Automatiser la création des tables via des scripts de migration SQL.

    C15 (Intégrer les ETL) : Utiliser Airflow pour orchestrer le passage des données de SeaweedFS (Landing) vers ClickHouse (DWH).

    C16 (Gérer le DWH) : Documenter la stratégie de sauvegarde (ex: snapshots ClickHouse déportés).

    C17 (Variations de dimensions) : Gèrer l'historisation des changements (ex: si une commune change de code postal, comment ton DWH garde l'historique ?).

## Bloc 4 : Collecte massive et Data Lake

On valorise l'aspect "Big Data" avec SeaweedFS.

    C18 (Architecture Data Lake) : Documenter l'architecture distribuée de SeaweedFS (Master, Volume, Filer).

    C19 (Intégration composants) : Configurer le connecteur S3 pour que ClickHouse puisse lire directement dans SeaweedFS.

    C20 (Catalogue de données) : Créer un "Data Dictionary" (un simple fichier Markdown ou une page Notion) répertoriant chaque fichier présent dans le Data Lake.

    C21 (Gouvernance) : Définir des règles d'accès (qui a le droit d'écrire/lire dans le bucket SeaweedFS) et documenter la suppression des données obsolètes.

## Bilan : Compétences couvertes ?

Toutes les compétences du référentiel sont couvertes par ce projet.

Attention particulière :

    E1 (C1) : Impérativement fournir les "grilles d'entretien" (les questions que l'on poserait aux utilisateurs).

    E6 (C16, C17) : Cette évaluation repose sur une étude de cas fictive sur la maintenance. S'assurer de simuler une panne (ex: "Que se passe-t-il si un disque sature ?") dans le rapport.
