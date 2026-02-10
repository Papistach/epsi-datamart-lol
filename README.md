# 🎮 League of Legends - Analyse de Parties

## 👥 Équipe
- Papis CISSOKO
- Ovina SAINT-MARTIN
- Ange Fleuryse MANANGANJI

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
- **Extract/Load** : PostgreSQL 15
- **Transform** : Jupyter Notebook (Python + pandas + SQLAlchemy)
- **Visualisation** : Jupyter Notebook

### Modèle relationnel (ERD)
![ERD](diagrams/erd.png)

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

# Exécuter dans l'ordre
# 1. 01_load_raw.ipynb
# 2. 02_transform.ipynb
# 3. 03_analysis.ipynb
```
