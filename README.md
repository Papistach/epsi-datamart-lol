# Atelier 1 - Statistiques de parties

# 🎮 League of Legends - Analyse de Parties

## 👥 Équipe
- Papis C
- Ovina ST-M
- Ange Fleuryse M
- Yohann L

## 📊 Jeu de données
**Source** : https://huggingface.co/datasets/AngryBacteria/league_of_legends/tree/main

**Description** : Dataset contenant les statistiques détaillées de matchs League of Legends incluant :
- Informations de match (durée, mode, version)
- Statistiques par joueur (kills, deaths, assists, gold)
- Champions joués
- Résultats des parties (victoire/défaite)

**Caractéristiques** :
- ~50,000+ matchs (fichier 1.5 GB)
- 10 joueurs par match (500,000+ participations)
- 160+ champions uniques
- Format JSON original converti pour analyse

**Note** : Les données originales sont au format JSON (API Riot Games). Conformément aux contraintes de l'atelier, un processus ELT (Extract-Load-Transform) est implémenté pour structurer les données en modèle relationnel.

## 🏗️ Architecture

### Stack technique
| Étape | Technologie | Description |
|-------|-------------|-------------|
| **Extract/Load** | PostgreSQL 15 | Stockage données brutes JSON + modèle relationnel |
| **Transform** | Jupyter Notebook (Python) | pandas, SQLAlchemy, ijson pour traitement 1.5GB |
| **Visualisation** | Jupyter Notebook | matplotlib, seaborn pour analyses et ### Infrastructure
- **Docker Compose** : PostgreSQL + Jupyter Notebook
- **Volume partagé** : Données et notebooks persistés
  
**Ci-dessous le ERD** :

![ERD](erd.png)

**Tables** :
- `MATCH` : Informations sur les parties
- `PLAYER` : Joueurs uniques
- `CHAMPION` : Champions du jeu
- `PARTICIPATION` : Lien entre matchs, joueurs et champions avec stats

---

## 🚀 Utilisation

### Prérequis
- Docker Desktop
- Git
- Fichier `match_v5.json` placé dans `data/raw/`

### Démarrage

#### 1. Cloner le repo

```bash
git clone https://github.com/TON_USERNAME/epsi-datamart-lol.git
cd epsi-datamart-lol
```

#### 2. Démarrer l'infrastructure
```bash
docker-compose up -d
```
#### 3. Accéder à Jupyter
URL : http://localhost:8888
Token : epsi2024

#### Exécuter dans l'ordre
 1. 01_load_raw.ipynb
 2. 02_transform.ipynb
 3. 03_analysis.ipynb
 4. 04_Player.ipynb

---
# Atelier 2 - Modèle dimensionnel

## Schéma en Étoile
![Schema Etoile](diagrams/diagram.png)

### Tables Dimensionnelles
| Table | Type | Description |
|-------|------|-------------|
| `dim_date` | Dimension | Temps (année, mois, jour, heure, week-end) |
| `dim_player` | Dimension | Joueurs avec segmentation (newbie, casual, regular, hardcore) |
| `dim_champion` | Dimension | Champions avec classe (Tank, Assassin, etc.) |
| `dim_map` | Dimension | Cartes de jeu (Summoners Rift, ARAM, etc.) |
| `fact_performance` | Fait | Mesures : kills, deaths, assists, gold, KDA, win/loss |

## Nouveaux Notebooks
| Notebook | Description |
|----------|-------------|
| `05_dimensional_model.ipynb` | Création du schéma en étoile |
| `06_etl_dimensions.ipynb` | Alimentation des dimensions et faits |
| `07_analyse_dimensionnelle.ipynb` | Analyses complexes (ex: winrate par classe sur carte X le week-end) |

## Exemple de Requête Analytique
```sql
-- Taux de victoire par classe de champion sur Summoners Rift le week-end
SELECT 
    dc.champion_class,
    COUNT(*) as games,
    ROUND(100.0 * SUM(CASE WHEN fp.win THEN 1 ELSE 0 END) / COUNT(*), 2) as winrate_pct
FROM fact_performance fp
JOIN dim_champion dc ON fp.champion_sk = dc.champion_sk
JOIN dim_map dm ON fp.map_sk = dm.map_sk
JOIN dim_date dd ON fp.date_id = dd.date_id
WHERE dm.map_name = 'Summoners Rift'
AND dd.is_weekend = true
GROUP BY dc.champion_class;
```
---
# Atelier 3 - Visualisation

Étape 1 : Intégration de Metabase
1.1 Modification du docker-compose.yml
Ajoutez deux services qui sont metabase et dozzle au fichier docker-compose.yml existant :
yaml

version: '3.9'

services:
  # ...  services existants (postgres, jupyter) ...
  
  # ==================== METABASE ====================
  metabase:
    image: metabase/metabase:latest
    container_name: lol_metabase
    environment:
      MB_DB_TYPE: postgres
      MB_DB_DBNAME: metabase
      MB_DB_PORT: 5432
      MB_DB_USER: metabase
      MB_DB_PASS: metabase_secret
      MB_DB_HOST: postgres
      JAVA_OPTS: "-Xmx2g"
    ports:
      - "3000:3000"
    depends_on:
      - postgres
    networks:
      - lol_network
    volumes:
      - metabase_data:/metabase-data

  # ==================== DOZZLE (Logs) ====================
  dozzle:
    image: amir20/dozzle:latest
    container_name: lol_dozzle
    environment:
      - DOZZLE_LEVEL=debug
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
    ports:
      - "8080:8080"
    networks:
      - lol_network

networks:
  lol_network:
    driver: bridge

volumes:
  # ... vos volumes existants ...
  metabase_data:
1.2 Création de la base Metabase dans PostgreSQL
Créez un script init-metabase.sql:
sql
-- Se connecter à postgres en tant que superuser
CREATE DATABASE metabase;
CREATE USER metabase_user WITH ENCRYPTED PASSWORD 'metabase_pw';
GRANT ALL PRIVILEGES ON DATABASE metabase TO metabase-_user;
1.3 Premier démarrage

# Démarrer la stack complète
docker-compose down  # si déjà en cours
docker-compose up -d

# Vérifier que Metabase est prêt 
docker logs -f metabase
Accédez à Metabase : http://localhost:3000
Configuration initiale :
Language : Français 
Email : fleuryse@local.fr
Mot de passe : root
Database connection :
Type : PostgreSQL
Host : postgres (nom du service docker)
Port : 5432
Database : test 
Username : user
Password : root
