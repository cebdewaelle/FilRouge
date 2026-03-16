# Transition_Energetique_Regionale

L'idée est de créer une plateforme qui compare la consommation réelle à la production d'énergies renouvelables (éolien/solaire) par région.  
  
    Le défi Data Engineer : Croiser des datasets massifs (Open Data Enedis + données météo historiques via API).

    Pipeline proposé (encore en sujet à changement) :

        Ingestion (Python) : Le script récupère les données Enedis et les "upload" sur SeaweedFS via le port 8333 en utilisant la bibliothèque boto3 (comme si c'était du AWS S3).  

        Stockage Brut (Bronze layer) : Les fichiers JSON/CSV/Parquet dorment sur SeaweedFS.  

        Transformation : Utilisation de dbt pour calculer des agrégats (conso moyenne par habitant vs production locale).  

        Intégration ClickHouse : Créer une table qui "pointe" directement sur le fichier dans SeaweedFS sans même l'importer physiquement au début.  

        Transformation : Création des tables finales (Silver/Gold) dans ClickHouse en utilisant le moteur MergeTree pour des agrégations ultra-rapides.  
    
  
    Le "petit plus" : Ajouter une dimension "température" pour constater la corrélation entre froid et pics de consommation.  


# L'Architecture Technique  
  
## Proposition de stack moderne : 
  
    Orchestration : Airflow (pour planifier l'ingestion tous les mois/jours).  
    Ingestion : Python (requêtes sur l'API Open Data Réseaux Énergies - ODRE).  
    Stockage (Data Lake) : [Seaweedfs](https://github.com/seaweedfs/seaweedfs) pour stocker les fichiers JSON/CSV/Parquet bruts (Raw Zone).  
    Transformation (Data Warehouse) : [Clickhouse](https://clickhouse.com/clickhouse) pour le SQL.  
    Visualisation : Metabase ou Streamlit.  


## Stratégie de Collecte et Modélisation

Le cœur du projet va être le croisement de trois sources de données distinctes :

    Données de Consommation (Enedis/GRDF) : Consommation annuelle ou trimestrielle par secteur (résidentiel, tertiaire, industriel) et par code IRIS (quartier).

    Données de Production (ODRE) : Installations de production d'électricité renouvelable (éolien, photovoltaïque) avec leur puissance installée et injectée.

    Données Externes (Météo France) : Températures moyennes et DJU (Degrés Jour Unifiés) pour normaliser la consommation (expliquer pourquoi on a consommé plus un hiver qu'un autre).


## Les Défis "Data Engineer" à mettre en avant

Pendant ta soutenance, tu devras insister sur les problèmes techniques que tu as résolus :
### A. Le Data Cleaning & Harmonisation

Les fichiers d'Enedis utilisent souvent des codes communes ou des codes IRIS.

    Challenge : Comment gérer les changements de codes postaux ou de noms de communes au fil du temps ?
    Solution DE : Créer une table de référence "Géographie" propre pour faire tes jointures sans perte de données.

### B. La gestion du "Incremental Load"

L'API Enedis contient des millions de lignes.

    Challenge : Ne pas tout recharger à chaque fois.
    Solution DE : Implémenter une stratégie d'ingestion incrémentale dans ton pipeline Airflow pour ne récupérer que les nouvelles données publiées.

### C. Calcul des KPIs avec dbt

Au lieu de faire des calculs complexes dans ton outil de BI, fais-les dans ton entrepôt de données :

    Taux d'auto-suffisance : Consommation Totale / Production EnR Locale​.
    Intensité Carbone : Estimation du CO2​ économisé grâce à la production locale.


# Scénario de Démo pour ta Soutenance

Imagine l'histoire que tu vas raconter :

    "Voici ma plateforme. Un décideur régional peut voir qu'en 2024, la région Occitanie a augmenté sa production solaire de 15%, mais que la consommation résidentielle a aussi bondi à cause d'un hiver rigoureux (données météo à l'appui). Mon pipeline a traité 2Go de données brutes, les a nettoyées et agrégées automatiquement via dbt pour fournir ce résultat en temps réel."



# Les données

Les données se trouvent ici:  
[Open data Enedis](https://data.enedis.fr/pages/opendatagrd/)  
[Agence ore](https://portail.agenceore.fr/pages/explore)


