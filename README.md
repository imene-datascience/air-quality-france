# 🇫🇷 Air Quality France — Analyse de la qualité de l'air

Analyse exploratoire et modélisation de l'indice de qualité de l'air (AQI) pour 802 villes françaises, à partir d'un jeu de données mondial sur la pollution atmosphérique.

## 🎯 Objectif du projet

Explorer les niveaux de pollution atmosphérique en France, identifier les villes les plus touchées, et comprendre la relation entre les polluants (PM2.5, NO2, Ozone, CO) et l'indice AQI global à travers deux approches de modélisation : régression linéaire (OLS) et Random Forest.

## 📊 Données

- **Source** : Global Air Pollution Dataset (23 463 villes, 12 colonnes)
- **Filtrage** : 802 villes françaises extraites et nettoyées
- **Variables clés** :
  - `aqi_value` : indice de qualité de l'air global
  - `pm2.5_aqi_value`, `no2_aqi_value`, `ozone_aqi_value`, `co_aqi_value` : sous-indices par polluant
  - `aqi_category` : catégorisation (Good, Moderate, Unhealthy...)

## 🧹 Nettoyage des données

- Correction d'un caractère invisible (tabulation) dans le nom de colonne `co_aqi_value`
- Filtrage sur `country_name == "France"`
- Vérification des valeurs manquantes
- Export du dataset nettoyé (`data/france_air_quality.csv`)

## 📈 Analyse exploratoire

Trois visualisations interactives (Plotly) générées dans `outputs/` :

| Fichier | Contenu |
|---|---|
| `01_distribution_aqi.html` | Distribution de l'AQI par catégorie |
| `02_top20_villes.html` | Top 20 des villes françaises les plus polluées |
| `03_comparaison_polluants.html` | Comparaison PM2.5 / NO2 / Ozone |

**Statistiques clés (802 villes) :**

| Indicateur | AQI | PM2.5 | NO2 | Ozone |
|---|---|---|---|---|
| Moyenne | 53.3 | 51.5 | 2.6 | 30.5 |
| Médiane | 50.0 | 50.0 | 2.0 | 28.0 |
| Max | 151.0 | 151.0 | 25.0 | 80.0 |

## 🤖 Modélisation

Deux modèles entraînés pour prédire l'AQI global à partir des sous-indices de polluants :

| Modèle | R² | MAE |
|---|---|---|
| Régression OLS | 0.958 | — |
| Random Forest | 0.999 | 0.18 µg/m³ |

Feature importance disponible dans `outputs/04_feature_importance.html`.

## ⚠️ Limite identifiée — Data Leakage

Le R² de 0.999 obtenu avec le Random Forest n'est **pas un signe de performance exceptionnelle**, mais révèle un problème de **fuite de données (data leakage)**, pour deux raisons combinées :

1. **Construction mathématique de l'AQI** : selon la méthodologie US EPA, l'indice global `aqi_value` est calculé comme **le maximum des sous-indices individuels** (PM2.5, NO2, Ozone, CO). Utiliser ces sous-indices comme variables prédictives revient donc à donner au modèle une information qui détermine quasi-directement la cible.
2. **Nature du dataset** : ce jeu de données (Kaggle "Global Air Pollution Dataset") ne provient pas de mesures de terrain vérifiées, mais semble généré/agrégé de façon synthétique. Il n'a donc pas le bruit et les incohérences qu'on trouverait dans de vraies données de capteurs, ce qui rend la relation entre sous-indices et AQI encore plus mécanique et facile à apprendre pour le modèle.

Le modèle n'apprend donc pas une vraie relation prédictive : il retrouve une règle de calcul sur des données propres et non-bruitées.

**Pourquoi je le documente ici plutôt que de le cacher :** identifier et expliquer ce biais est plus révélateur d'une compréhension solide en data science qu'un score parfait présenté sans recul. Une vraie tâche prédictive nécessiterait des données de mesures réelles et des features indépendantes du calcul de l'AQI (météo, trafic, densité urbaine, saison, etc.).

## 🛠️ Stack technique

- **Python** : pandas, statsmodels, scikit-learn
- **Visualisation** : Plotly Express
- **Modélisation** : régression OLS (statsmodels), Random Forest (scikit-learn)

## 📁 Structure du projet

```
air-quality-france/
├── data/
│   ├── global_air_pollution_data.csv
│   └── france_air_quality.csv
├── notebooks/
│   └── 01_exploration.ipynb
├── outputs/
│   ├── 01_distribution_aqi.html
│   ├── 02_top20_villes.html
│   ├── 03_comparaison_polluants.html
│   └── 04_feature_importance.html
└── README.md
```

## 🚀 Pour reproduire

```bash
git clone https://github.com/imene-datascience/air-quality-france.git
cd air-quality-france
pip install pandas plotly statsmodels scikit-learn
jupyter notebook notebooks/01_exploration.ipynb
```

## 🔭 Pistes d'amélioration

- Corriger le data leakage en excluant les sous-indices utilisés dans le calcul officiel de l'AQI, et tester des features réellement indépendantes (météo, saison, zone géographique)
- Ajouter une validation croisée pour fiabiliser les métriques
- Explorer une dimension temporelle si les données le permettent
- Cartographier les résultats (ex. Folium / GeoPandas) pour une visualisation géographique de la pollution
