# FTS Cameroun — Prédiction du financement humanitaire par cluster

📊 **Projet Data & Machine Learning end-to-end**  
Ce projet utilise les données du **Financial Tracking Service (FTS)** concernant les **besoins** et le **financement** humanitaire au Cameroun (par cluster mondial et par année).  
L’objectif est de **prédire le taux de financement futur par cluster** afin d’identifier à l’avance les secteurs les plus à risque de sous-financement.

---

## 🎯 Objectif métier
- **Contexte** : Les appels humanitaires des Nations Unies présentent souvent un écart important entre les besoins exprimés (requirements) et les financements effectivement reçus (funding).  
- **Problème** : Anticiper ces écarts permet de mieux planifier les actions de plaidoyer et d’orienter les financements vers les clusters les plus vulnérables.  
- **Question posée** : Peut-on prédire le **taux de financement (`funding_rate`)** ou l’**écart de financement (`gap`)** pour l’année suivante, à partir de l’historique ?

---

## 📂 Données
Source : **FTS — OCHA (Financial Tracking Service)**  
- **Granularité** : par *global cluster* × année, Cameroun uniquement.  
- **Variables clés** :
  - `requirements` : besoins exprimés (USD)
  - `funding` : financement reçu (USD)
  - `funding_rate = funding / requirements`
  - `gap = requirements - funding`
- **Dimensions** :
  - Colonnes : `year`, `cluster`, `requirements`, `funding`, `funding_rate`, `gap`
  - Horizon temporel : plusieurs années, multi-clusters

---

## 🔧 Pipeline ML

1. **Exploration des données (EDA)**
   - Évolution des besoins et financements par année.
   - Variabilité inter-clusters.
   - Graphiques Requirements vs Funding.

2. **Feature engineering**
   - Variables de retard (*lags*) : `funding_rate_lag1`, `gap_lag2`, …
   - Dynamiques annuelles : croissance YoY (`req_yoy`, `fund_yoy`).
   - Tendances récentes : pente sur 2 ans (`slope2`).
   - Encodage catégoriel du cluster (`OneHotEncoder`).

3. **Modélisation**
   - **Baseline** : persistance (reprendre `funding_rate_lag1`).
   - **Régressions** : 
     - `Ridge` (linéaire, interprétable).
     - `RandomForestRegressor` (non-linéaire, robuste).

4. **Validation**
   - Split temporel : train = années anciennes, test = dernière année.
   - Validation **walk-forward (TimeSeriesSplit)** pour simuler la vraie prévision.

5. **Évaluation**
   - MAE (Mean Absolute Error).
   - MAPE (Mean Absolute Percentage Error).
   - Comparaison aux baselines.

6. **Interprétation**
   - Importance des features par permutation.
   - Analyse des clusters où l’erreur est la plus élevée.

7. **Export**
   - Pipeline complet sauvegardé avec `joblib`.
   - Notebook reproductible (end-to-end).

---

## 📈 Résultats
- Le **RandomForest** améliore la baseline de persistance.
- Les **lags de financement et de taux de financement** sont les variables les plus prédictives.
- Le modèle met en évidence certains clusters systématiquement sous-financés.

---

## 🚀 Prolongements possibles
- Passer en **classification binaire** : prédire si un cluster sera sous-financé (`funding_rate < 60%`).
- Intégrer des **données externes** : contexte économique, conflits, climat.
- Déployer une **mini-app Streamlit** pour visualiser les prévisions par cluster.
- Générer un **rapport automatique** (Papermill/nbconvert) pour diffusion aux décideurs.

---

## 🛠️ Stack technique
- **Python** (Pandas, NumPy, Matplotlib, Scikit-learn)
- **Jupyter Notebook** (exploration et prototypage)
- **Export modèle** : `joblib`


---

## 💡 Les compétences mis en avec ce projet

- Capacité à **transformer un jeu de données métier** en un problème ML pertinent.  
- Maîtrise d’un pipeline **end-to-end** : EDA → features → modèle → validation → export.  
- Respect des **bonnes pratiques temporelles** (éviter la fuite de données).  
- Capacité à **communiquer clairement** les résultats et leur utilité opérationnelle.  

---

✍️ Réalisé par Anicet Diderot K. KADJI — *Data Enthusiast*  
