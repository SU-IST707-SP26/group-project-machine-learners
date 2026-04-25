# Work Directory — Notebook Index

This directory contains all supporting Jupyter notebooks for the project. Notebooks are organized roughly in pipeline order: data collection → EDA → feature engineering → modeling → evaluation.

---

## Data Collection

### `10-K_filings_data_pull.ipynb`
Downloads 10-K annual filings from SEC EDGAR for all 709 companies in the ESG dataset and extracts three key narrative sections: Item 1 (Business), Item 1A (Risk Factors), and Item 7 (MD&A). Extraction skips table-of-contents matches and reads until the next section boundary. Produces 332 companies with complete 3-section extractions, saved as JSON files in `data/extracted_sections/`.

### `10-K_filings_extraction_quality_check.ipynb`
Audits the quality of the extracted 10-K sections across all 332 modeling companies. Checks for: 500k character cap hits, XBRL markup contamination (financial data tags extracted instead of prose), and high duplicate sentence rates. Classifies each company as clean / borderline / critical and saves results to `data/structured_features/extraction_quality.csv`. Identified 9 critical bad-extraction companies that are filtered out before modeling.

### `structured_features_collection.ipynb`
Fetches FY2021 financial fundamentals for all 332 companies via the SEC EDGAR Company Facts API. Collects 8 raw balance-sheet and income-statement features per company (total assets, revenue, net income, long-term debt, stockholders' equity, operating income, cash equivalents, public float), derives 3 ratio features (debt-to-assets, profit margin, return on equity), and log-transforms scale features. Saves 332 × 17 feature matrix to `data/structured_features/sec_fundamentals.csv`.

---

## Exploratory Data Analysis

### `EDA_ESG.ipynb`
Full EDA on the Kaggle ESG ratings dataset (709 companies). Covers: score and grade distributions across all four pillars (total, environment, social, governance), industry composition and imbalance, pillar correlation heatmap, outlier detection, and temporal analysis confirming the dataset is a 2022 cross-sectional snapshot.

### `EDA_10-K_filings.ipynb`
EDA on FinBERT-extracted features from the 10-K filings. Key analyses include: zero-variance and near-universal feature identification, sentence cap floor effect for Social/Governance pillars, within-industry bimodality investigation of the environment score distribution, company size vs. ESG score association, UMAP vs. PCA visualization of the FinBERT feature space (coloured by grade and industry), and grade distribution analysis across industries.

### `EDA_Structured_Features.ipynb`
Analyzes the relationship between company financial fundamentals and ESG scores. Produces scatter plots of log total assets vs. ESG scores per pillar (with Pearson r), quartile box plots showing score distributions by company size, and a bimodal threshold analysis comparing companies above vs. below the 500 environment score boundary using Mann-Whitney U tests.

---

## Feature Engineering and Modeling

### `Feature_Extraction_and_Modeling.ipynb`
Main modeling pipeline. Covers the full sequence from raw features to trained models:
- **Steps 7.1–7.5**: data loading, feature engineering (drop zero-variance features, clip outliers, log-transform keyword frequencies, add industry one-hot dummies)
- **Step 7.6**: merge SEC structured financial features (5 features; industry-median imputation)
- **Step 7.7**: extraction quality filter removing 9 critical bad-extraction companies (332 → 323)
- **Step 7.8**: disclosure threshold analysis — shallow decision tree to reverse-engineer the rating algorithm's bimodal decision rule
- **Step 8**: trains Ridge, LassoCV, ElasticNetCV, and XGBoost across all four ESG targets (total, environment, social, governance) with 5-fold CV
- **Additional sections**: SHAP threshold analysis for the environment score boundary; sentence count cap analysis; feature combination ablation (FinBERT only / structured only / industry+structured / FinBERT+industry / full)

### `hyperparameter_tuning.ipynb`
Tunes XGBoost and Ridge hyperparameters for all four ESG targets. Uses `RandomizedSearchCV` (40 iterations × 5-fold CV) over 8 XGBoost parameters and `RidgeCV` over 50 alpha values on a log scale. Produces improvement heatmaps and ΔR² bar charts. Best XGBoost parameters saved to `data/finbert_features/best_xgboost_params.csv`.

### `model_comparison_and_grade_classifier.ipynb`
Unified comparison of Ridge, Lasso, ElasticNet, and XGBoost across all four ESG targets using consistent 80/20 split and 5-fold CV. Also implements: (1) score-to-grade conversion using empirical grade boundaries derived from the dataset, and (2) class-weighted Logistic Regression and XGBoost classifiers to address BBB class imbalance (~52% of the dataset). Results saved to `data/finbert_features/model_comparison_results.csv`.

### `shap_explainability_analysis.ipynb`
SHAP explainability analysis on the tuned XGBoost model for all four ESG targets. Produces summary plots, bar plots, waterfall plots, dependence plots, and industry-level SHAP breakdowns. Full feature importance table saved to `data/finbert_features/shap_feature_importance_all_targets.csv`.

---

## Final Model Evaluation

### `final_model_validation.ipynb`
Validates the final selected model (ElasticNet) on the held-out test set using 323 companies (post extraction quality filter) and 108 features. Produces: non-zero coefficient plots, shared feature heatmap across pillars, and industry R² breakdown. Current holdout results — Social: R²=0.173, Environment: R²=0.078, Total: R²=0.065, Governance: R²≈0.000 (0 features selected; governance is determined by board/shareholder data not present in 10-K filings).
