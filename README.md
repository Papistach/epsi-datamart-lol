# 🎮 League of Legends - Analyse de Parties

## 👥 Équipe
- Papis CISSOKO

## 📊 Jeu de données
**Source** : [Lien vers ton dataset]

**Description** : Dataset contenant les statistiques détaillées de matchs League of Legends incluant :
- Informations de match (durée, mode, version)
- Statistiques par joueur (kills, deaths, assists, gold)
- Champions joués

**Caractéristiques** :
- X matchs
- Y joueurs uniques
- Z champions

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
