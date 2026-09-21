# AI-Driven Explainable Credit Risk Modelling on AMEX Data

End-to-end, explainable credit risk modelling on the AMEX time-series dataset, using feature-interaction graphs and CNN-based feature analysis. The pipeline goes from raw data to an audit-ready customer report: a Databricks medallion data layer, thousands of engineered features narrowed to 23, an XGBoost default model, a CIBIL-style credit score, and explanations and automation on top.

Developed during an internship in the Advanced Quantz & Analytics (AQuA) team, BFSI, at Tata Consultancy Services (TCS).

## Highlights

- **0.98 ROC-AUC** and **93% Precision-Recall** for default prediction
- **5,385 engineered features** reduced to a **23-feature** model (22 base + 1 interaction feature)
- Feature selection combining **SHAP, Boruta, a Genetic Algorithm, CNN permutation importance and a Neo4j interaction graph**
- Predicted probability of default converted into **CIBIL-style credit scores** with risk bands
- **SHAP, LIME, DiCE and GenAI** for audit-ready, customer-level reports
- **Power BI dashboard** and an **n8n + FastAPI** workflow from loan application to email trigger

## Results

The final model achieved a **ROC-AUC of 0.98** and a **Precision-Recall score of 93%**.

Final candidates (XGBoost, XGBoost + Platt scaling, a soft-voting ensemble and CatBoost) were compared on the same untouched validation split (data split 70/15/15 into train, validation and test):


All four candidates performed almost identically, so **XGBoost** was chosen as the final model: it is the simplest to tune, deploy and explain, with no meaningful loss in accuracy. Platt scaling was tested and gave no measurable calibration gain (identical Brier score and log loss to raw XGBoost).

## Pipeline overview

1. Data engineering (Databricks, PySpark)
2. Feature screening and engineering
3. Feature selection and interaction discovery
4. Class imbalance handling
5. Model development and selection
6. Explainability and reporting
7. Deployment and automation

## Methodology

### 1. Data engineering
- Built a Databricks **Medallion architecture** (bronze, silver, gold layers) in PySpark on the AMEX time-series dataset.
- Integrated the Databricks **Feature Store** so engineered features are reusable and versioned.

### 2. Feature screening and engineering
- Screened the raw features with **univariate, bivariate and multivariate analysis**, including correlation filtering, factor analysis and clustering (191 → 156 columns).
- Engineered customer-level predictive features across six windowed feature families (156 → 5,385 features).

### 3. Feature selection and interaction discovery
- Ranked and reduced features with **SHAP, Boruta, a Genetic Algorithm and CNN permutation importance**, applying a one-standard-error parsimony rule to choose the final set.
- Used a **Neo4j feature-interaction graph** to discover and validate interacting features.
- Final feature set: **23 features** (22 base features + 1 interaction feature).

### 4. Class imbalance
- Compared **ADASYN** and **CTGAN** for balancing the training data.

### 5. Model development and selection
The modelling ran in two stages, with **Optuna** used for hyperparameter tuning.

- **Earlier stage** (after data balancing, before interaction features were added): compared Logistic Regression, LightGBM, XGBoost, CatBoost and dense neural networks.
- **Final stage** (with the final feature set): compared XGBoost, XGBoost with Platt scaling, a soft-voting ensemble and CatBoost on an untouched validation split (see Results above). XGBoost was selected for simplicity, since performance was comparable across candidates.

### 6. Explainability and reporting
- Converted predicted probability of default (PD) into **CIBIL-style credit scores** with risk bands.
- Explained individual decisions with **SHAP** and **LIME**, and generated counterfactual "what would change this outcome" analysis with **DiCE**.
- Used **GenAI** to turn these outputs into audit-ready, customer-level reports.

### 7. Deployment and automation
- Built a **Power BI dashboard** for portfolio-level monitoring.
- Built an **n8n workflow** that takes a new customer's loan application, scores it through a **FastAPI** service (exposed with ngrok for demos) and triggers an email with the result.

## Tech stack

Databricks, PySpark, XGBoost, LightGBM, CatBoost, Optuna, SHAP, LIME, DiCE, Boruta, Neo4j, ADASYN, CTGAN, FastAPI, n8n, ngrok, Power BI

## Limitations and future work

- Fairness analysis and out-of-time validation are needed before production deployment.
  
## Note

This repository documents the project. The dataset used is the public AMEX Default Prediction dataset.

