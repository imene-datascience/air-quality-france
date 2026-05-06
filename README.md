# Analyse de la qualité de l'air en France 🌍

## Contexte
Analyse exploratoire et modélisation statistique de la qualité de l'air 
dans 802 villes françaises à partir de données open data.

## Résultats clés
- PM2.5 est le polluant dominant (importance > 95% en Random Forest)
- Modèle OLS : R² = 0.958
- Random Forest : R² = 0.999, MAE = 0.18 µg/m³
- Top villes polluées : Pertuis, Saint-Martin-de-Crau, Autun

## Stack technique
Python 3.11 · Pandas · Plotly · Statsmodels · Scikit-learn · Jupyter

## Graphiques interactifs
- Distribution de l'AQI par catégorie
- Top 20 villes les plus polluées
- Comparaison des polluants (boxplot)
- Feature importance Random Forest

## Lancer le projet
pip install -r requirements.txt
jupyter notebook
