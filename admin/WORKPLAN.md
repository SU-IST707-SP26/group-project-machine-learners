# WORKPLAN.md

## Active Plan

### Milestone 1: Data Identification and Project Proposal
- [✅] M1.T1 — Find and Download Kaggle ESG dataset (Team)
- [✅] M1.T2 — Ensure SEC 10-K filing data can be retrieved through Python library (Ethan)
- [✅] M1.T4 — Complete and push proposal along with management docs (Ethan)

### Milestone 2: Data Verification and Quality Assessment
- [✅] M2.T1 — Collect SEC 10-K files for companies in ESG dataset, must filter for temporal variable (Ethan)
- [✅] M2.T2 — Verify target features from ESG dataset - numeric scores and letter grades (Caden)
- [✅] M2.T3 — Assess text quality and length distribution across 10-K sections to validate FinBERT input feasibility (Ethan/Caden)
- [✅] M2.T4 — Complete EDA on ESG dataset, identify potential class/industry imbalances (Ethan)

### Milestone 3: Preprocessing Pipeline, Understand 10-K Structure, FinBERT Operational
- [✅] M3.T1 — Collect and extract Business, Risk Factors, and MD&A sections from 10-K filings for all companies in ESG dataset (Ethan/Caden)
- [✅] M3.T2 — Audit extracted sections for quality, length distribution, and partial extractions (Caden)
- [✅] M3.T3 — Merge extracted 10-K data with ESG dataset on ticker symbol (Caden)
- [✅] M3.T4 — Complete first test of FinBERT model with extracted text data, verify embeddings generate correctly (Caden)
- [✅] M3.T5 — EDA on extracted 10-K filing features: validate FinBERT sentiment features, keyword distributions, document structure, and feature-target correlations (Caden)

### Milestone 4: FinBERT Embeddings Generated, Train/Test Split, Baseline Model Output
- [✅] M4.T1 — Generate FinBERT embeddings for all three sections across all 332 companies with complete filings (Ethan)
- [✅] M4.T2 — Combine FinBERT features with company metadata: industry one-hot encoding, document structure features (Ethan/Caden)
- [✅] M4.T3 — Perform 80/20 train/test split (Caden)
- [✅] M4.T4 — Train baseline Ridge Regression and XGBoost models; evaluate with 5-fold CV (Caden)
- [✅] M4.T5 — Apply EDA-driven feature engineering: drop zero-variance and near-universal features, log-transform keyword frequencies, clip pos_neg_ratio outliers, add one-hot industry dummies (Caden)
- [✅] M4.T6 — Complete and submit checkpoint/submission.ipynb with all rubric sections (Team)

### Milestone 5: Multiple Models Trained/Compared and Best Model Identified
- [✅] M5.T1 — Train additional models: Lasso/ElasticNet with LassoCV, Gradient Boosting; reframe target as industry-relative residuals (Team) — LassoCV/ElasticNetCV results recorded; ElasticNet selected as best model (CV R² beats XGBoost across all targets)
- [✅] M5.T2 — Tune hyperparameters on top performing models (John)
- [✅] M5.T3 — Compare models using consistent evaluation metrics (RMSE, R², F1) (John)
- [✅] M5.T4 — Apply SHAP values to best model for explainability analysis (John)
- [✅] M5.T5 — Convert predicted scores to letter grades and document final model selection (John)
- [⏳] M5.T6 — Raise FinBERT sentence cap to 300+ for Social/Governance pillars and re-run feature extraction (Ethan) — deferred; identified as a potential improvement before finalizing the model
- [✅] M5.T7 — Apply class weighting to grade classifier to address BBB class imbalance (John)
- [✅] M5.T8 — Investigate within-industry bimodality of environment score distribution; check whether grade distribution is also bimodal within industries (Caden)
- [✅] M5.T9 — SHAP-based threshold analysis: identify features that discriminate companies just below vs. at/above the 500 environment score boundary (Caden)
- [✅] M5.T10 — UMAP visualization of FinBERT feature space; compare against PCA for non-linear cluster structure; colour by grade and industry (Caden)
- [✅] M5.T11 — Evaluate sentence count columns (E/S/G_sentence_count) as features; ablation test and SHAP importance check (Caden)
- [✅] M5.T12 — Investigate non-semantic features as supplements to FinBERT: market cap, revenue, employee count, and other structured company-level signals (Caden)
- [✅] M5.T13 — Revisit problem framing: evaluate whether grade classification (B/BB/BBB/A) outperforms ESG score regression given the confirmed bimodal structure (Caden)
- [❌] M5.T14 — Analyze EU vs. US company ESG score distributions to test mandatory-reporting hypothesis as driver of bimodality (Ethan) — closed, no compatible external dataset available; disclosure threshold analysis in Step 7.8 pursues this directly
- [✅] M5.T15 — Integrate extraction_quality.csv flag into modeling pipeline: filter or down-weight the 9 critical bad-extraction companies (COST, AKTS, NTAP, ACNB, DXCM, EBAY, TER, AOS, LOW) and assess impact on R² (Caden/Ethan) — confirmed 332 → 323 companies after filter
- [✅] M5.T16 — Run feature combination ablation experiment (cell added to Feature_Extraction_and_Modeling.ipynb) and record results; identify optimal feature subset (FinBERT only / structured only / combined) (Caden/Ethan)

### Milestone 6: Final Model Validated, Evaluation, Explainability Framework
- [⏳] M6.T1 — Validate final model on holdout test set and report all evaluation metrics (Caden) — first look complete in final_model_validation.ipynb; two feature improvements pending before treating as final: (1) section-presence features, (2) raised sentence cap
- [⏳] M6.T2 — Coefficient analysis and feature explainability for ElasticNet final model (Caden) — non-zero coefficient plots and shared feature heatmap produced in final_model_validation.ipynb; XGBoost SHAP analysis by John in shap_explainability_analysis.ipynb remains valid as a comparison
- [⏳] M6.T3 — Analyze model performance across industries and ESG pillars (Caden) — industry R² heatmap and breakdown produced in final_model_validation.ipynb; will update after feature improvements
- [ ] M6.T4 — Document explainability findings and compare against commercial ESG rating methodology (X)
- [ ] M6.T5 — Add section-presence binary features from 10-K filings (did each section exist? section length buckets?) as structural signals and evaluate impact on model performance (X)
- [⏳] M6.T6 — Re-run final_model_validation.ipynb after feature improvements (M5.T6 sentence cap + M6.T5 section features) to produce final holdout metrics (Caden)

### Milestone 7: Visualizations, Final Report, Presentation
- [ ] M7.T1 — Build visualizations for model performance, SHAP values, and ESG score distributions (X)
- [⏳] M7.T2 — Write final report covering methodology, results, and limitations (Team) — Title, Team, Introduction, and Literature Review drafted in final/submission.md; remaining sections placeholders
- [✅] M7.T3 — Prepare and deliver Checkpoint 3 presentation (Team) — submitted as presentation/submission.pdf

---

## Changelog

### 2026-02-03
- (Ethan) ⏳ M1.T3 — SEC 10-K filing data must be filtered so it is from before the ESG data from Kaggle; Kaggle dataset from throughout 2022

### 2026-02-23
- (Ethan) ⏳ M2.T3 — 10-K filing data extraction for business, mda, and risk sections is computationally intensive, may need to optimize operations or pipeline if computation with FinBERT becomes extreme as well.

### 2026-02-28
- (Ethan) ⏳ M2.T4 — ESG dataset EDA revealed environmental score bimodality and industry imbalance that will need to be addressed during feature engineering and model training.

### 2026-03-02
- (Ethan) ⏳ M3.T1 — Initial extractions complete for roughly 629 full companies (all 3 sections) and 11 partial companies (at least 1 section), need to audit and validate extracted json files.

### 2026-03-04
- (Ethan/Caden) ✅ M3.T1 — Finalized 10-K section extraction pipeline: 332 companies with complete 3/3 sections, 12 partial, 25 failures. All results stored in data/extracted_sections/.
- (Caden) ✅ M3.T2 — Ran extraction quality check: Business ~67,600 chars, Risk Factors ~123,100 chars, MD&A ~96,000 chars average. 12 partial extractions identified and excluded from modeling.
- (Caden) ✅ M3.T3 — Merged ESG labels with extracted section metadata; merged_features_labels.csv created.

### 2026-03-06
- (Caden) ✅ M3.T4 — FinBERT feature extraction complete for all 332 companies (~3.5 hours CPU, batch inference). 76 features per company generated and cached to data/finbert_features/.
- (Caden) ✅ M4.T1 — FinBERT features saved to data/finbert_features/finbert_features.csv.
- (Caden) ✅ M4.T2 — Features merged with ESG labels in merged_features_labels.csv; ready for modeling.

### 2026-03-07
- (Caden) 🆕 M3.T5 — Added EDA task on extracted 10-K filing features to validate feature quality before modeling.
- (Caden) ✅ M3.T5 — EDA revealed 6 zero-variance features, extreme pos_neg_ratio outliers (max ~9M), right-skewed keyword frequencies, and 68-79% of companies hitting the 100-sentence cap for Social/Governance pillars; filled out work/EDA_10-K_filings.ipynb.
- (Caden) ✅ M4.T3 — 80/20 train/test split applied; 5-fold CV used for robust performance estimates.
- (Caden) ✅ M4.T4 — Ridge baseline: Test R² = −0.12. XGBoost baseline: Test R² = −0.10, 5-CV R² = +0.054. Feature engineering improvements raised both from original −0.27 / −0.14.
- (Caden) 🆕 M4.T5 — Added feature engineering step based on EDA recommendations.
- (Caden) ✅ M4.T5 — Applied: dropped 11 zero-variance/near-universal presence features, clipped pos_neg_ratio at 99th percentile, log-transformed 14 keyword frequency features, added 38 one-hot industry dummies → 103 final features.
- (Caden) ✅ M4.T6 — checkpoint/submission.ipynb completed with all rubric sections: Overview, Data + EDA, Preprocessing, Modeling, Problems & Challenges, Next Steps.
- (Team) 🆕 M5.T6 — Added task to raise FinBERT sentence cap; EDA confirmed floor effect for Social/Governance features (68-79% hit cap).
- (Team) 🆕 M5.T7 — Added task to apply class weighting to grade classifier; BBB class is 52% of dataset causing prediction bias.
- (Caden) 🔄 M5.T1 — Updated scope: added Lasso/ElasticNet with LassoCV and industry-relative residual target formulation based on baseline model analysis showing R² ceiling with current approach.

### 2026-04-10
- (Caden) 🔄 M5.T12 — Attempted employee count from EDGAR DEI namespace; coverage 1/332, reverted. Using log_total_assets as size proxy.
- (Caden) 🔄 M5.T12 — Reduced Step 7.6 structured features from 9 → 5 to limit multicollinearity (dropped log_total_revenue, log_stockholders_equity, log_cash_and_equivalents, log_long_term_debt). Feature matrix: 112 → 108.
- (Caden) 🆕 M5.T15 — Added task: integrate bad extraction flag into modeling pipeline; 9 critical companies identified (XBRL contamination or TOC-stub MDA).
- (Caden) 🆕 M5.T16 — Added task: run feature combination ablation (FinBERT only / Structured only / Industry+Structured / FinBERT+Industry / Full); cell added to Feature_Extraction_and_Modeling.ipynb.
- (Caden) 🆕 — Created work/EDA_Structured_Features.ipynb: company size (log_total_assets) vs ESG score scatter plots, quartile box plots, and bimodal threshold analysis.
- (Caden) 🆕 — Added 10-K extraction quality audit to EDA_10-K_filings.ipynb: cap-hit analysis, duplicate sentence rates, XBRL contamination detection, spot-check previews, bad extraction flag saved to data/structured_features/extraction_quality.csv.

### 2026-04-05
- (Caden) ✅ M5.T12 — Built SEC EDGAR Company Facts pipeline: fetched FY2021 fundamentals for all 332 companies (0 failures), saved to data/structured_features/sec_fundamentals.csv. 9 features merged into Feature_Extraction_and_Modeling.ipynb Step 7.6 with industry-median imputation. Feature matrix: 103 → 112 features.
- (Caden) ✅ M5.T13 — Decided to retain continuous regression targets. Bimodal structure will be examined directly rather than collapsed into binary classes; continuous model preserves signal for explainability.
- (Caden) 🆕 M5.T14 — Added EU vs. US ESG score distribution analysis task to directly test the mandatory-reporting hypothesis for bimodality.

### 2026-03-27
- (Caden) ✅ M5.T8 — Added within-industry bimodality analysis to EDA_10-K_filings.ipynb: per-industry KDE/histogram plots, % above-500 bar chart, company size (doc_length) Mann-Whitney test, and threshold feature comparison (400–499 vs 500–599 bands). Bimodality persists within industries, implicating company size and rater threshold effects.
- (Caden) ✅ M5.T8 — Extended bimodality analysis to total_grade classification: overall grade distribution, per-industry grade bar charts, stacked proportion chart, doc_length by grade (Kruskal-Wallis), and feature means heatmap by grade.
- (Caden) ✅ M5.T9 — Added threshold SHAP analysis to Feature_Extraction_and_Modeling.ipynb: computed SHAP values for all companies in 400–599 band, mean SHAP difference bar chart, side-by-side beeswarms, raw distribution histograms with Mann-Whitney tests, and SHAP decision plot.
- (Caden) ✅ M5.T10 — Added UMAP section to EDA_10-K_filings.ipynb: PCA vs UMAP side-by-side coloured by grade, UMAP coloured by industry, and PCA scree plot showing variance explained per component.
- (Caden) ✅ M5.T11 — Added sentence count analysis to Feature_Extraction_and_Modeling.ipynb: confirmed E/S/G_sentence_count in feature set, plotted cap-at-100 distributions, correlation heatmap vs ESG scores, SHAP importance ranking, and 5-fold CV ablation (with vs. without sentence counts).
- (Caden) 🆕 M5.T12 — Added task to investigate non-semantic structured features (market cap, revenue, etc.) as supplements to FinBERT features.
- (Caden) 🆕 M5.T13 — Added task to revisit classification vs. regression framing given confirmed bimodal grade structure.

### 2026-04-11
- (Ethan) 🆕 — Added Step 7.7 to Feature_Extraction_and_Modeling.ipynb: extraction quality filter removes 9 critical bad-extraction companies (332 → 323). Implements M5.T15.
- (Ethan) 🆕 — Added Step 7.8 to Feature_Extraction_and_Modeling.ipynb: disclosure threshold analysis using decision tree (depth=3 and depth=5) to reverse-engineer the rating algorithm's bimodal decision rule for environment score.
- (Ethan) 🔄 M5.T1 — Expanded `train_and_evaluate` in Step 8 to include LassoCV and ElasticNetCV alongside Ridge and XGBoost. Four-model comparison table with automatic alpha/l1_ratio selection and non-zero feature counts.
- (Ethan) ✅ M5.T16 — Documented feature combination ablation results in markdown cell below heatmap with interpretation of all 5×4 combinations.
- (Ethan) ❌ M5.T14 — Searched Kaggle and open data sources for pre-2020 ESG dataset to test methodology change hypothesis; no compatible dataset found (different providers, scales, or insufficient non-US companies). Closed in favor of disclosure threshold analysis in Step 7.8.

- ### 2026-04-13
- (John) ✅ M5.T3 — Built unified model comparison notebook: trained Ridge, Lasso, ElasticNet, XGBoost across all four ESG targets with consistent 80/20 split and 5-fold CV. Produced heatmap, bar chart, and scatter plot visualizations. Results saved to data/finbert_features/model_comparison_results.csv.
- (John) ✅ M5.T5 — Implemented score_to_grade() conversion function using empirical grade boundaries derived from dataset. Evaluated conversion accuracy with confusion matrix and per-grade accuracy breakdown.
- (John) ✅ M5.T7 — Implemented class-weighted Logistic Regression and XGBoost classifier to address BBB class imbalance (~52% of dataset). Used StratifiedKFold and compute_sample_weight('balanced'). Per-class F1 improved for minority grades.
- (John) 🆕 M5.T2 — Added hyperparameter tuning notebook: RandomizedSearchCV over 40 iterations × 5-fold CV for XGBoost; RidgeCV over 50 alpha values. Best params saved to data/finbert_features/best_xgboost_params.csv.

- ### 2026-04-13
- (John) ✅ M5.T2 — RandomizedSearchCV over 40 iterations × 5-fold CV for XGBoost; RidgeCV over 50 alpha values. Best params saved to data/finbert_features/best_xgboost_params.csv.

- - (John) ✅ M5.T4 — SHAP explainability analysis complete for all four ESG targets using tuned XGBoost. Summary, bar, waterfall, dependence, and industry-level plots produced. Full importance table saved to data/finbert_features/shap_feature_importance_all_targets.csv.


### 2026-04-16
- (Caden) ✅ M5.T1 — LassoCV/ElasticNetCV results recorded after full notebook run; ElasticNet selected as final model over XGBoost.
- (Caden) ✅ M5.T15 — Extraction quality filter confirmed; 332 → 323 companies after removing 9 critical bad-extraction companies.
- (Caden) ⏳ M6.T1 — First look at final model complete in final_model_validation.ipynb (ElasticNet, 323 companies, 108 features). Social Test R²=0.173, Environment=0.078, Total=0.065, Governance≈0.000 (0 features selected). Two improvements identified before treating as final.
- (Caden) ⏳ M6.T2 — Coefficient analysis produced; XGBoost SHAP from John remains valid comparison.
- (Caden) ⏳ M6.T3 — Industry R² heatmap produced; will update after feature improvements.
- (Caden) 🆕 M6.T5 — Added task: section-presence binary features from 10-K filings as new structural signals.
- (Caden) 🆕 M6.T6 — Added task: re-run final_model_validation.ipynb after M5.T6 + M6.T5 improvements.

### 2026-04-19
- (Ethan) 🆕 M7.T3 — Added Checkpoint 3 presentation task; 8-slide structure planned covering problem, solution, data, initial results (ElasticNet first look), challenges, and plan & goals. Due 4/27.
- (Ethan) 🔄 M7.T3 — Slide structure and framing decisions documented in worklog. Results slides pending final feature improvements and model re-run before deck is locked.

### 2026-04-25
- (Ethan) ✅ M7.T3 — Checkpoint 3 presentation complete and submitted as presentation/submission.pdf. 8-slide deck covering problem/stakeholder, envisioned solution, data, initial results (ElasticNet Model Evaluation + ElasticNet Model Findings), challenges, and plan & goals. Results slides built around final_model_validation.ipynb outputs.
- (Caden) 🆕 — Created work/README.md: structured index of all 11 supporting notebooks organized into Data Collection, EDA, Feature Engineering and Modeling, and Final Model Evaluation sections. Directly addresses final rubric requirement for a notebook index.

### 2026-05-03
- (Ethan) ⏳ M7.T2 — Began drafting final/submission.md. Title, Team, Introduction, and Literature Review complete. Data and Methods, Supporting Files, Results, Discussion, Limitations, Future Work sections remain as placeholders.