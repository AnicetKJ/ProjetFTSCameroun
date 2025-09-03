# FTS Cameroun — Etude du financement humanitaire par catégorie

📊 **Projet Data & Machine Learning end-to-end**  
Ce projet utilise les données du **Financial Tracking Service (FTS)** concernant les **besoins** et le **financement** humanitaire au Cameroun (par cluster mondial et par année).  
L’objectif est de **prédire le taux de financement futur par cluster** afin d’identifier à l’avance les secteurs les plus à risque de sous-financement.

---

## 🎯 Objectif métier
- **Contexte** : Les appels humanitaires des Nations Unies présentent souvent un écart important entre les besoins exprimés (requirements) et les financements effectivement reçus (funding).  
- **Problème** : Anticiper ces écarts permet de mieux planifier les actions de plaidoyer et d’orienter les financements vers les clusters les plus vulnérables.  
- **Question posée** : Peut-on prédire le **taux de financement** ou l’**écart de financement** pour l’année suivante, à partir de l’historique ?

---

## 📂 Données
Source : **FTS — OCHA (Financial Tracking Service)**  
- **Granularité** : par *cluster* par année, Cameroun uniquement.  
- **Variables clés** :
  - `requirements` : besoins exprimés (USD)
  - `funding` : financement reçu (USD)
  - `funding_rate = funding / requirements`
  - `gap = requirements - funding`
- **Dimensions** :
  - Colonnes : `year`, `cluster`, `requirements`, `funding`, `funding_rate`, `gap`
  - Horizon temporel : plusieurs années, multi-clusters

---

## 🔧 Pipeline ML (ce qui m'est proposé)

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

---

Ces catégories (clusters) représentent les différents secteurs humanitaires qui demandent des financements au Cameroun. Je vais les regrouper ici par thématiques principales :

1. **Services de base**
   - `Water Sanitation Hygiene` (Eau, Assainissement, Hygiène)
   - `Health` (Santé)
   - `Education` (Education)
   - `Nutrition` (Nutrition)
   - `Food Security` (Sécurité alimentaire)

2. **Protection & Services sociaux**
   - `Protection` (général)
   - `Protection - Child Protection` (Protection de l'enfance)
   - `Protection - Gender-Based Violence` (Violence basée sur le genre)
   - `Protection - Housing, Land and Property` (Logement, Terre et Propriété)
   - `Protection - Human Trafficking & Smuggling` (Traite des êtres humains)
   - `Protection - Mine Action` (Déminage)

3. **Infrastructure & Logistique**
   - `Emergency Shelter and NFI` (Abris d'urgence et biens non alimentaires)
   - `Logistics` (Logistique)
   - `Camp Coordination / Management` (Gestion des camps)

4. **Support & Coordination**
   - `Coordination and support services` (Services de coordination)
   - `Multi-sector` (Multi-secteurs)
   - `Multipurpose Cash` (Aide monétaire polyvalente)

5. **Relèvement & Développement**
   - `Agriculture` (Agriculture)
   - `Early Recovery` (Relèvement précoce)

6. **Autres**
   - `COVID-19` (Réponse COVID-19)
   - `Not specified` (Non spécifié)
   - `Multiple clusters/sectors (shared)` (Clusters/secteurs multiples)
   - `Other` (Autres)