Est-ce que tu veux que je te prépare un petit bout de code Python pour envoyer ton premier fichier sur SeaweedFS ?



Parfait ! Pour que ton script Python puisse parler à SeaweedFS comme s'il s'agissait d'un vrai bucket AWS S3, on va utiliser la bibliothèque boto3.

C'est un excellent point pour ta soutenance : tu montres que ton code est "Cloud-agnostic". S'il fonctionne avec ton SeaweedFS local, il fonctionnera sur AWS avec quasiment zéro modification.
1. Préparation

Installe les dépendances nécessaires :
Bash

pip install boto3 pandas pyarrow

2. Le script d'ingestion (Python)

Ce script simule l'étape de "Landing" : il récupère une donnée (ici un DataFrame factice ou un CSV Enedis) et l'envoie vers ton Data Lake local.
Python

import boto3
import pandas as pd
from io import BytesIO

# 1. Configuration de la connexion S3 vers SeaweedFS
# Note : 'http://localhost:8333' correspond au port S3 du docker-compose
s3 = boto3.client(
    's3',
    endpoint_url='http://localhost:8333',
    aws_access_key_id='any',     # SeaweedFS accepte n'importe quoi par défaut
    aws_secret_access_key='any',
    region_name='us-east-1'      # Obligatoire pour boto3, même en local
)

def upload_to_datalake(df, bucket_name, file_name):
    # Création du bucket s'il n'existe pas
    try:
        s3.create_bucket(Bucket=bucket_name)
    except:
        pass # Le bucket existe déjà

    # Conversion du DataFrame en Parquet (mémoire)
    out_buffer = BytesIO()
    df.to_parquet(out_buffer, index=False)
    
    # Upload vers SeaweedFS
    s3.put_object(
        Bucket=bucket_name, 
        Key=file_name, 
        Body=out_buffer.getvalue()
    )
    print(f"✅ Fichier {file_name} envoyé sur SeaweedFS dans le bucket {bucket_name}")

# --- EXEMPLE D'UTILISATION ---
# On imagine que c'est ton dataset Enedis
data = {
    'horodatage': ['2024-01-01 00:00', '2024-01-01 01:00'],
    'consommation_mw': [450.5, 442.2],
    'region': ['Occitanie', 'Occitanie']
}
df_enedis = pd.DataFrame(data)

upload_to_datalake(df_enedis, "raw-zone", "enedis/conso_2024.parquet")

3. Comment ClickHouse récupère la donnée ?

Une fois que le fichier est dans SeaweedFS, tu n'as même pas besoin de Python pour le mettre dans ClickHouse. Tu peux le faire directement en SQL via l'interface DB-Gate (port 3000) ou DBeaver.

Voici la commande SQL à exécuter dans ClickHouse pour créer une table qui "lit" ton fichier :
SQL

CREATE TABLE enedis_raw
ENGINE = S3('http://s3:8333/raw-zone/enedis/conso_2024.parquet', 'any', 'any', 'Parquet')
AS SELECT * FROM s3('http://s3:8333/raw-zone/enedis/conso_2024.parquet', 'any', 'any', 'Parquet');

Note : On utilise http://s3:8333 car à l'intérieur du réseau Docker, ClickHouse appelle le service par son nom défini dans le docker-compose.
💡 Le conseil "Expert" pour ton dossier :

Dans ton rapport, explique que tu as choisi le format Parquet pour le stockage dans SeaweedFS car :

    Il conserve le schéma (types de colonnes).

    Il est compressé (gain de place sur ton disque).

    ClickHouse le lit de manière native et vectorisée (ultra rapide).

