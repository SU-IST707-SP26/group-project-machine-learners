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
- FinBERT sentence cap raised to 300 per pillar (was 100); re-run produces `finbert_features.csv` used by all downstream notebooks

### `structural_features_extended.ipynb`
Extracts readability and document-structure features from raw 10-K section text for all 323 modeling companies. Per section (business, risk_factors, mda): word count, uncapped sentence count (regex splitter), average sentence length, Flesch-Kincaid grade level (textstat), and quantitative density (numeric mentions per 1,000 words). Derives section-level `is_long` bucket flags, `mda_is_quantitative`, `total_word_count`, and `mean_fk_grade`. Output: `data/structured_features/extended_structural_features.csv` (323 × 22 cols).

---

## Final Model Evaluation

### `final_model_validation.ipynb`
Validates the final selected model (ElasticNet) on the held-out test set using 323 companies and ~130 features. Feature set: 300-cap FinBERT sentiment features + SEC structured financials + extended structural features (readability, doc length, quantitative density) + `mode_prob_high` soft split feature. Produces: non-zero coefficient plots, feature group contribution chart (8 groups), and industry R² breakdown. Final holdout results — Social: R²=+0.215, Total: R²=+0.062, Environment: R²=+0.019, Governance: R²=+0.006. CV R² (same training data / alpha): total=+0.247, env=+0.311, social=+0.142, gov=−0.008 (treat as upper bound due to circularity).

---

## Archive

The following notebooks are preserved for reference but are not part of the active pipeline. They document approaches that were evaluated and either superseded or absorbed into other notebooks.

### `archive/tfidf_lsa_features.ipynb`
Fits a TF-IDF → TruncatedSVD (LSA) pipeline on 10-K corpus text for all 323 companies. LSA: 50 latent topic components (61% corpus variance explained). LSA features were trialed in the final model but excluded after causing a regularization cascade that degraded test-set performance. Finding: document topic patterns from 10-K vocabulary do not improve ESG score prediction beyond what FinBERT sentiment already captures.

### `archive/mode_split_modeling.ipynb`
Investigates bimodal ESG score structure via mode-split modeling. Oracle mode-split (perfect mode assignment) achieves environment R²=+0.74, confirming the bimodal structure is real and learnable in principle. However, the mode classifier reached only 61.5% test accuracy, making hard mode assignment harmful in practice. The soft `mode_prob_high` feature extracted from this classifier was absorbed into `final_model_validation.ipynb` instead.

### `archive/hyperparameter_tuning.ipynb`
Tunes XGBoost and Ridge hyperparameters for all four ESG targets using `RandomizedSearchCV` (40 iterations × 5-fold CV) and `RidgeCV`. ElasticNet was ultimately selected as the final model, making XGBoost tuning results informational only. Best XGBoost parameters saved to `data/finbert_features/best_xgboost_params.csv`.

### `archive/shap_explainability_analysis.ipynb`
SHAP explainability analysis on the tuned XGBoost model for all four ESG targets. Produces summary plots, bar plots, waterfall plots, dependence plots, and industry-level SHAP breakdowns. Not used in the final report because SHAP was run on XGBoost, not the final ElasticNet model. ElasticNet coefficients serve as feature importance in the final report instead.

### `archive/model_comparison_and_grade_classifier.ipynb`
Unified comparison of Ridge, Lasso, ElasticNet, and XGBoost across all four ESG targets. Also implements score-to-grade conversion using empirical grade boundaries and a class-weighted grade classifier to address BBB class imbalance (~52% of the dataset). ElasticNet was selected as the final model; grade conversion logic was absorbed into `final_model_validation.ipynb`.
