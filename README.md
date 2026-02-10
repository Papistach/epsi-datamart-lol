#Atelier 1 - Statistiques de parties

# 🎮 League of Legends - Analyse de Parties

## 👥 Équipe
- Papis C
- Ovina ST-M
- Ange Fleuryse M
- Yohann L il dois 50 € à chaqu'un

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

## 🚀 Utilisation
```bash
# Démarrer l'environnement
docker-compose up -d

# Accéder à Jupyter
# Récupérer le token dans les logs
docker logs game_notebook



**Tables** :
- **`raw_matches`** : Stockage JSON brut (étape ETL initiale)
- **`match`** : Informations sur les parties (clé primaire `match_id`)
- **`player`** : Joueurs uniques identifiés par `player_puuid`
- **`champion`** : Dictionnaire des champions (id + nom)
- **`participation`** : Table de faits liant matchs, joueurs, champions avec statistiques détaillées

---

## 🚀 Utilisation

### Prérequis
- Docker Desktop
- Git
- Fichier `match_v5.json` placé dans `data/raw/`

### Démarrage

```bash
# 1. Cloner le repo
git clone https://github.com/TON_USERNAME/epsi-datamart-lol.git
cd epsi-datamart-lol

# 2. Démarrer l'infrastructure
docker-compose up -d

# 3. Accéder à Jupyter
# URL : http://localhost:8888
# Token : epsi2024

# Exécuter dans l'ordre
# 1. 01_load_raw.ipynb
# 2. 02_transform.ipynb
# 3. 03_analysis.ipynb
# 4. 04_Player.ipynb
