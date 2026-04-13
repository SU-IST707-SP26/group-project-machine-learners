# WORKLOG.md

## 2026-04-13 — Hyperparameter Tuning (John)

**Context**: Following model comparison work, implemented hyperparameter tuning for XGBoost and Ridge across all four ESG targets (M5.T2).

**Work Completed**:

*Hyperparameter Tuning — `work/hyperparameter_tuning.ipynb` (M5.T2)*
- Used `RandomizedSearchCV` with 40 iterations × 5-fold CV to search over 8 XGBoost hyperparameters per target: `n_estimators`, `max_depth`, `learning_rate`, `subsample`, `colsample_bytree`, `reg_lambda`, `reg_alpha`, `min_child_weight`.
- Used `RidgeCV` over 50 alpha values on a log scale (0.01–1000) for Ridge tuning.
- Recorded baseline vs tuned performance for all model × target combinations.
- Produced improvement heatmap, ΔR² bar chart, and CV score distribution plots showing hyperparameter sensitivity per target.
- Best XGBoost parameters saved to `data/finbert_features/best_xgboost_params.csv` for use in final model training (M6.T1).
- Key finding: tuning improves environment and total score prediction; governance remains negative after tuning confirming it is a data limitation rather than a modeling problem.

**Files Created**:
- `work/hyperparameter_tuning.ipynb`

**Files Generated** (on notebook run):
- `data/finbert_features/hyperparameter_tuning_results.csv`
- `data/finbert_features/best_xgboost_params.csv`

**Next Steps**: Use best params from `best_xgboost_params.csv` for final model validation (M6.T1). Run full SHAP analysis on tuned best model (M6.T2). Analyze model performance across industries and pillars (M6.T3).

## 2026-04-13 — Model Comparison, Score-to-Grade Conversion, Class-Weighted Grade Classifier (John)

**Context**: Picking up unassigned Milestone 5 tasks — building a unified model comparison, implementing score-to-grade conversion, and addressing the BBB class imbalance in the grade classifier.

**Work Completed**:

*Model Comparison — `work/model_comparison_and_grade_classifier.ipynb` (M5.T3)*
- Trained all four models (Ridge, Lasso, ElasticNet, XGBoost) across all four ESG targets (total, environment, social, governance) with consistent 80/20 split and 5-fold CV.
- Built unified comparison table with Test R², Test MAE, Test RMSE, and 5-CV R² for all model × target combinations.
- Produced three visualizations: heatmap of Test R² and CV R², grouped bar chart of CV R² by target, and scatter plot of R² vs MAE trade-off per target.
- Key finding: XGBoost is the best model across all targets; governance remains negative for every model confirming it requires external board/shareholder data.

*Score-to-Grade Conversion — `work/model_comparison_and_grade_classifier.ipynb` (M5.T5)*
- Derived empirical grade boundaries from minimum total_score per grade in the dataset.
- Implemented `score_to_grade()` function converting continuous predicted scores to letter grades (B/BB/BBB/A/AA).
- Evaluated conversion accuracy on XGBoost test predictions with confusion matrix and per-grade accuracy breakdown.

*Class-Weighted Grade Classifier — `work/model_comparison_and_grade_classifier.ipynb` (M5.T7)*
- Confirmed BBB class dominance (~52% of dataset) causes naive classifiers to predict BBB for everything.
- Implemented `class_weight='balanced'` Logistic Regression and XGBoost with `compute_sample_weight('balanced')`.
- Used StratifiedKFold to preserve class proportions across folds.
- Produced side-by-side confusion matrices and per-class F1 comparison chart showing minority grade recall improvement with class weighting.

**Files Created**:
- `work/model_comparison_and_grade_classifier.ipynb`

**Next Steps**: Tune hyperparameters on top performing models (M5.T2). Validate final model on holdout test set (M6.T1). Run full SHAP analysis on best model (M6.T2).

## 2026-04-11 — Extraction Quality Filter, Lasso/ElasticNet Models, Ablation Documentation, Disclosure Threshold Analysis (Ethan)

**Context**: Following the 04-10 extraction audit and feature ablation, today focused on four threads: (1) integrating the bad extraction flag into the modeling pipeline, (2) adding Lasso and ElasticNet models to the model comparison, (3) documenting the ablation results, and (4) building a disclosure threshold analysis to reverse-engineer the rating algorithm's decision rule driving bimodality.

**Work Completed**:

*Extraction Quality Filter — `Feature_Extraction_and_Modeling.ipynb` Step 7.7*
- Added Step 7.7 after the structured features merge (Step 7.6) to load `extraction_quality.csv` and remove 9 critical bad-extraction companies (COST, AKTS, NTAP, ACNB, DXCM, EBAY, TER, AOS, LOW).
- 2 borderline companies (ARE, STX) retained — features degraded but not fully corrupted.
- Modeling dataset: 332 → 323 companies. Cell is in place; results pending next full notebook run.

*Lasso/ElasticNet Models — `Feature_Extraction_and_Modeling.ipynb` Step 8*
- Replaced the `train_and_evaluate` function to include four models: Ridge, LassoCV, ElasticNetCV, and XGBoost.
- LassoCV tests 100 alpha values with 5-fold CV for automatic regularisation selection; L1 penalty performs automatic feature selection by driving weak features to exactly zero.
- ElasticNetCV searches over both alpha and l1_ratio (7 values: 0.1–0.99) to find the optimal L1/L2 blend.
- Results table now prints all four models with test R², MAE, 5-CV R², plus Lasso/ElasticNet-specific details (selected alpha, l1_ratio, number of non-zero features).
- Results pending next full notebook run.

*Ablation Results Documentation — `Feature_Extraction_and_Modeling.ipynb`*
- Added markdown interpretation cell below the feature combination ablation heatmap documenting the 5×4 ablation results from the 04-10 session.
- Key findings documented: FinBERT alone is negative across all targets; 5 structured features outperform 65 FinBERT features for environment; Full combination is the only positive result for social (+0.049); governance is negative in every combination.

*Disclosure Threshold Analysis — `Feature_Extraction_and_Modeling.ipynb` Step 7.8*
- Added Step 7.8 after extraction quality filter to reverse-engineer the rating algorithm's disclosure threshold driving the bimodal environment score distribution.
- Hypothesis: the bimodality across all four score distributions is driven by the rating provider's treatment of disclosure (binary reported vs. not reported) rather than genuine ESG performance differences, amplified by the 2020 methodology change assigning zeros for unreported metrics.
- Approach: shallow decision tree (depth=3) classifies companies as above/below 500 environment score using the full engineered feature set. Tree splits reveal the feature thresholds separating the two modes.
- Also fits a deeper tree (depth=5) for comparison, prints classification reports, and extracts specific threshold values from tree nodes.
- Results pending next full notebook run.

*Pre-2020 ESG Data Search*
- Searched Kaggle and open data sources for a pre-2020 ESG dataset from the same or comparable rating provider to test the 2020 methodology change hypothesis.
- No feasible dataset found: Tunguz's Kaggle ESG dataset is country-level (World Bank), not company-level; S&P 500 ESG Risk Ratings uses Sustainalytics (different methodology and scale); FTSE Russell dataset (GitHub) covers 2014–2018 but uses a 0–5 scale incompatible with our 0–1000 scoring.
- Current dataset is almost entirely US companies (691/709 USD-denominated), limiting any EU vs US comparison to ~18 companies — too few for statistical conclusions.
- Concluded that the bimodality is most likely an artifact of the rating provider's disclosure threshold (binary reported vs. not reported) rather than a temporal methodology shift that could be tested with external data. The disclosure threshold analysis (Step 7.8) pursues this explanation directly using existing features.
- M5.T14 closed — insufficient external data available to test the mandatory-reporting hypothesis.

**Files Modified**:
- `work/Feature_Extraction_and_Modeling.ipynb` (Step 7.7 extraction filter added; Step 7.8 disclosure threshold analysis added; Step 8 `train_and_evaluate` expanded with Lasso/ElasticNet; ablation results markdown added)

**Next Steps**: Run full notebook to record results from Steps 7.7, 7.8, 8, and 11 with the filtered 323-company dataset. If disclosure threshold tree achieves high accuracy, consider adding a binary disclosure feature to the model. Raise FinBERT sentence cap to 300+ and re-run feature extraction (M5.T6). Tune and compare top models (M5.T2, M5.T3).

## 2026-04-10 — Multicollinearity Reduction, EDA Structured Features, Feature Ablation, Extraction Quality Audit (Caden)

**Context**: Following the 04-05 structured features integration, today focused on four threads: (1) tightening the structured feature set to reduce multicollinearity, (2) building EDA to visualise the company size hypothesis, (3) adding a feature combination ablation experiment, and (4) auditing the 10-K extraction pipeline for data quality issues that may be corrupting FinBERT features.

**Work Completed**:

*Company Size Feature — Employee Count Attempt*
- Attempted to add `EntityNumberOfEmployees` from the SEC EDGAR DEI namespace as a direct company size proxy.
- Coverage was 1/332 companies (effectively zero) — DEI employee count is not reliably XBRL-tagged in annual 10-K filings. Reverted all changes to `structured_features_collection.ipynb`.
- Settled on `log_total_assets` as the primary size proxy (99% coverage, canonical balance-sheet scale measure).

*Multicollinearity Reduction — `Feature_Extraction_and_Modeling.ipynb` Step 7.6*
- Reduced `STRUCTURED_COLS` from 9 → 5 features to limit multicollinearity among the log-transformed size variables.
- **Dropped**: `log_total_revenue`, `log_stockholders_equity`, `log_cash_and_equivalents`, `log_long_term_debt` (all highly correlated with `log_total_assets`; `log_long_term_debt` also has 51% missingness).
- **Kept**: `log_total_assets` (size), `log_public_float` (market-cap proxy, not fully redundant with assets), `debt_to_assets`, `profit_margin`, `return_on_equity` (ratios — orthogonal to scale).
- Feature matrix: 112 → 108 features.

*EDA Structured Features — `work/EDA_Structured_Features.ipynb`* (new notebook)
- Created new notebook to directly visualise the company size hypothesis.
- Plot 1: 2x2 scatter plots — ESG score (x) vs `log_total_assets` (y) per pillar, with regression line and Pearson r/p-value annotated.
- Plot 2: Score distribution by size quartile (Q1-Q4) — box plots per pillar with group means overlaid; prints mean score table per quartile.
- Plot 3: Bimodal threshold analysis — box + strip plots comparing `log_total_assets` for companies above/below the 500 environment score threshold, with Mann-Whitney U test.
- Considered a two-model approach (separate models for each bimodal peak, analogous to a hardcoded first decision tree split on size) but decided against it — the size breakdown did not show a clean enough separation to justify the added complexity.

*Feature Combination Ablation — `Feature_Extraction_and_Modeling.ipynb`*
- Added a new section at the bottom of the modeling notebook running 5-fold CV XGBoost R2 for all four ESG targets across five feature combinations: FinBERT only, Structured only, Industry + Structured (no text), FinBERT + Industry (no structured), and Full (current 108 features).
- Results displayed as a live progress log + pivot table + RdYlGn heatmap. Cell is ready to run; results to be recorded in next session.

*10-K Extraction Quality Audit — `work/EDA_10-K_filings.ipynb`*
- Added three new sections at the bottom of the EDA notebook:
  1. **Audit cell**: scans all 344 extracted section files for 500k cap hits, XBRL contamination, and duplicate sentence rates. Outputs summary statistics + two plots (section length bar charts with cap line; duplicate rate histogram + worst-offender bar chart).
  2. **Spot-check cell**: for all 21 cap-hit modeling companies, prints a summary table (lengths + issue flags) and 400-char text preview of each section — allows direct visual classification of real prose vs. broken extraction.
  3. **Bad extraction flag cell**: saves `data/structured_features/extraction_quality.csv` with severity flag (0 = clean, 1 = borderline, 2 = critical) for all 332 modeling companies.

*Key Extraction Audit Findings*
- 30 sections across 21 modeling companies hit the 500k character cap.
- **9 critical extractions**: COST and AKTS (XBRL markup extracted instead of text); ACNB, DXCM, EBAY, TER, AOS (2-section cap + ~162-char TOC-stub MDA — extractor lost position in filing after hitting cap twice); LOW (2-section cap + 11.6% duplicate sentence rate).
- **2 borderline**: ARE (business section only 3,233 chars — likely missed section start); STX (business section hit cap, unusual pattern).
- **321 clean** (96.7% of modeling set). Mean duplicate sentence rate across all filings: 1.54%.
- COST is the most severe: FinBERT ran over base64-encoded XBRL financial data tags rather than narrative text — all sentiment features for COST are meaningless.

**Domain Note**: 2020 ESG methodology change — raters switched from giving companies benefit of the doubt on unreported metrics to assigning 0s for unreported data. This is a strong candidate explanation for the bimodal distribution: the methodology change structurally deflates scores for companies that do not report comprehensively, pushing partial reporters into the lower mode.

**Files Created**:
- `work/EDA_Structured_Features.ipynb` (company size vs ESG score visualisations)
- `data/structured_features/extraction_quality.csv` (bad extraction flag — generated when audit cell is run)

**Files Modified**:
- `work/structured_features_collection.ipynb` (reverted employee count addition)
- `work/Feature_Extraction_and_Modeling.ipynb` (Step 7.6 reduced to 5 features; feature combination ablation section added)
- `work/EDA_10-K_filings.ipynb` (extraction audit, cap-hit spot-check, and bad extraction flag sections added)

**Next Steps**: Run feature combination ablation and record results. Integrate `extraction_quality.csv` into the modeling pipeline — filter or flag the 9 critical companies and assess impact on R2. Consider re-extracting broken filings from source or raising the 500k cap. Potentially explore EU or pre 2020 ESG scores to test hypothesis on a lack of reporting leading to bimodality.

## 2026-04-05 — Bimodality Research, SEC EDGAR Structured Features Pipeline (Caden)

**Context**: Following the 03-27 feedback response, two threads were pursued: (1) deeper domain research into the causes of the bimodal ESG score distribution to inform modeling decisions, and (2) implementing the structured company-level features identified in M5.T12 using the SEC EDGAR Company Facts API.

**Work Completed**:

*Bimodality Domain Research*
- Decided to retain continuous regression targets rather than reframing as binary classification (resolves M5.T13) — the bimodal structure is worth modeling directly, and a continuous model preserves more information for explainability.
- Researched candidate explanations for the bimodal environment score distribution:
  - **Selective disclosure**: companies only report on metrics they are scored on, leaving unreported metrics as zero structurally inflates scores for comprehensive reporters and deflates scores for partial reporters.
  - **EU mandatory reporting**: EU regulations require disclosure on certain ESG metrics where the US does not; if EU companies cluster above 500 this would explain the upper mode. Supporting literature: https://www.sciencedirect.com/science/article/pii/S104244312500023X
  - **Company size as dominant predictor**: larger companies have dedicated ESG teams and report more comprehensively, pushing them into the upper mode. Supporting literature: https://esgthereport.com/the-barriers-to-esg-reports-for-midsize-small-cap-companies/ and https://www.pm-research.com/content/pmrjesg/1/4/31
  - **Threshold incentives**: companies may be incentivized to hit specific score thresholds (e.g., required by ESG-linked financing) rather than optimize the continuous score, creating a natural clustering effect.
  - **Government mandates**: regulatory floors for governance disclosure raise the lower bound of governance score distributions, contributing to the bimodal shape.
- Identified new next steps: (1) examine EU vs. US company score distributions as a direct test of the reporting-mandate hypothesis, (2) add disclosure rate / reporting completeness as a feature, (3) consider separate models or reporting-indicator variables.

*SEC EDGAR Structured Features — `work/structured_features_collection.ipynb`*
- Created `work/structured_features_collection.ipynb` to fetch FY2021 10-K financial fundamentals for all 332 modeling companies via the SEC EDGAR Company Facts API (`https://data.sec.gov/api/xbrl/companyfacts/`).
- Used FY2021 data (not current) to maintain temporal alignment with the ESG ratings dataset, which was processed in April 2022 based on FY2021 filings.
- Fetched 8 raw features per company: `total_assets`, `total_revenue`, `net_income`, `long_term_debt`, `stockholders_equity`, `operating_income`, `cash_and_equivalents`, `public_float` (market cap proxy from DEI namespace).
- Revenue extraction uses a 7-concept fallback list to handle different XBRL tagging conventions across industries.
- Derived 3 ratio features: `debt_to_assets`, `profit_margin`, `return_on_equity`.
- Log-transformed all 6 size/scale features to normalize across orders of magnitude.
- Ran notebook: 332/332 companies fetched with 0 HTTP failures. Coverage: `total_assets` 99%, `total_revenue` 95%, `public_float` 98%, `long_term_debt` 49% (expected — many companies carry no long-term debt or use non-standard tags).
- Saved to `data/structured_features/sec_fundamentals.csv` (332 × 17 features).
- Added EDA: distribution plots for all features and Pearson correlation heatmap vs. all four ESG score targets.

*Structured Features Integration — `work/Feature_Extraction_and_Modeling.ipynb`*
- Added Step 7.6 to `Feature_Extraction_and_Modeling.ipynb` to merge SEC fundamentals into the modeling pipeline after the existing Step 7.5 feature engineering.
- Imputation strategy: industry-median first, then global-median fallback — chosen over global-median-only because size and leverage vary substantially across industries.
- Ratio extremes winsorised at 1st/99th percentile before merging.
- Step 7.6 is gracefully skipped if `sec_fundamentals.csv` is not yet present, so the notebook remains runnable without the structured features.
- Ran notebook: 332/332 companies matched, all 9 features merged with zero missing values post-imputation. Engineered feature matrix expanded from 103 → 112 features. All downstream steps (model training, SHAP, per-pillar analysis) use the expanded set automatically.

*Model Performance — Before vs. After Structured Features (same notebook, same train/test split)*

Total score XGBoost test R² improved from negative to positive; environment score CV R² improved meaningfully. Governance remains a poor fit with both feature sets.

| | **103 features (FinBERT only)** | | **112 features (+SEC structured)** | |
|---|---|---|---|---|
| **Model / Pillar** | **Test R²** | **5-CV R²** | **Test R²** | **5-CV R²** |
| Ridge (total_score) | −0.122 | −0.013 | −0.167 | +0.028 |
| XGBoost (total_score) | −0.101 | +0.054 | **+0.080** | **+0.061** |
| XGBoost — Environment | −0.009 | +0.100 | **+0.147** | **+0.138** |
| XGBoost — Social | −0.145 | +0.028 | **+0.038** | **+0.055** |
| XGBoost — Governance | −0.316 | −0.079 | −0.248 | −0.152 |

Key observations:
- **XGBoost total score test R²**: −0.101 → +0.080 (+0.181 swing) — the structured features pushed the model into positive predictive territory for the first time.
- **Environment score**: largest absolute gain; CV R² +0.100 → +0.138. Consistent with company size being a dominant driver of environment disclosure quality.
- **Social score**: test R² recovered from −0.145 to +0.038 — meaningful improvement though still weak.
- **Governance score**: still negative in both runs. The structured financial features do not capture what drives governance ratings, suggesting governance is determined by board composition, shareholder structure, and policy disclosures not present in 10-K financials or FinBERT sentiment.
- **Ridge degraded slightly** on test R² (−0.122 → −0.167) while CV R² improved; this is consistent with Ridge struggling to leverage the new non-linear size signals that XGBoost can exploit.

**Files Created**:
- `work/structured_features_collection.ipynb` (SEC EDGAR fetch, EDA, and save pipeline)
- `data/structured_features/sec_fundamentals.csv` (332 companies × 17 features)

**Files Modified**:
- `work/Feature_Extraction_and_Modeling.ipynb` (added Step 7.6 — structured features merge and imputation)

**Impact**: M5.T12 complete. M5.T13 resolved (retaining continuous regression). Feature matrix now includes company-scale signals that directly test the company-size hypothesis for bimodality. The 9 new features cost no additional modeling infrastructure — they are automatically picked up by the existing `prepare_modeling_data` function.

**Next Steps**: Run EU vs. US score distribution analysis to test the mandatory-reporting hypothesis (new). Raise FinBERT sentence cap to 300+ and re-run feature extraction (M5.T6). Train additional models: Lasso/ElasticNet and Gradient Boosting with industry-relative residual targets (M5.T1). Tune and compare top models (M5.T2, M5.T3).


## 2026-03-27 — Feedback Response: Bimodality Investigation, SHAP Threshold Analysis, UMAP, Sentence Count Ablation (Caden)

**Context**: Received checkpoint feedback noting that (1) the bimodal environment score distribution was not fully explained and should be investigated within industries, (2) UMAP should be tried to find non-linear structure in the FinBERT feature space that PCA may miss, (3) the 100-sentence cap likely hurts model quality, and (4) the spike at 500 on environment score looks like a rater threshold effect rather than a natural distribution — identifying what decision rule drives it is a key next step.

**Work Completed**:

*Bimodality Analysis — `EDA_10-K_filings.ipynb`*
- Added full within-industry bimodality analysis: per-industry KDE + histogram panels (all industries with ≥10 companies), 500-threshold line on each, plus a summary bar chart of % of companies above 500 per industry. Confirmed bimodality persists within several individual industries, ruling out industry mix as the sole explanation.
- Added company size proxy analysis: scatter plot and box plot of `doc_length` vs. above/below 500 groups with Mann-Whitney U test. Larger filings (company size proxy) are associated with crossing the threshold.
- Added threshold feature comparison: compared mean feature values for companies in the 400–499 band vs. the 500–599 band; identified the top discriminating features by absolute mean difference and % difference.
- Added `total_grade` classification analysis: overall grade distribution bar chart, binary Low (B/BB) vs High (BBB/A) split, per-industry grade bar charts, stacked proportional bar chart sorted by % high grades, doc_length box plots by grade (Kruskal-Wallis test), and a heatmap + grouped bar chart of the top 15 features by A–B mean difference.

*SHAP Threshold Analysis — `Feature_Extraction_and_Modeling.ipynb`*
- Added new section using the trained `environment_score` XGBoost model to compute SHAP values for all companies in the 400–499 and 500–599 bands (not just the test split).
- Mean SHAP difference bar chart (above − below 500) identifies which features the model uses to push predictions across the threshold — the candidates for the rater's decision rule.
- Side-by-side SHAP beeswarm plots for each band show how SHAP distributions shift at the threshold.
- Raw feature distribution histograms for the top 5 discriminating features with Mann-Whitney U tests and median comparisons.
- SHAP decision plot for all threshold-band companies, with above-500 companies highlighted and the threshold marked.

*UMAP — `EDA_10-K_filings.ipynb`*
- Added UMAP section using FinBERT sentiment features, pillar scores, coverage percentages, and sentence counts as the feature matrix.
- PCA (2D) vs. UMAP (2D) side-by-side, both coloured by `total_grade` — directly tests whether UMAP reveals grade clusters that PCA misses.
- Second UMAP panel coloured by industry — separates whether grade or industry is the dominant organising principle in the feature space.
- PCA scree plot (per-component and cumulative variance) shows how many dimensions are needed to capture 90%/95% of variance, validating the professor's suggestion to use more than 2 PCA components.

*Sentence Count Feature Analysis — `Feature_Extraction_and_Modeling.ipynb`*
- Confirmed `E_sentence_count`, `S_sentence_count`, `G_sentence_count` are present in the dataset and checked whether they are in the current `feature_cols`.
- Plotted distributions of all three with the cap-at-100 line annotated; `S_sentence_count` and `G_sentence_count` are heavily capped (many companies hit 100), while `E_sentence_count` varies meaningfully.
- Correlation heatmap of sentence counts vs. all four ESG score targets.
- SHAP importance bar chart highlighting where the sentence count columns rank among all features in the environment model (coloured orange).
- 5-fold CV ablation: R² with vs. without sentence count features, directly answering the professor's question about whether they add value. If Δ R² ≈ 0, the cap-induced ceiling is confirmed as the bottleneck and raising it is the fix.

**Files Modified**:
- `work/EDA_10-K_filings.ipynb` (added bimodality, grade, UMAP, and scree sections)
- `work/Feature_Extraction_and_Modeling.ipynb` (added SHAP threshold analysis and sentence count analysis)

**Impact**: M5.T8, M5.T9, M5.T10, M5.T11 complete. Directly addresses all four points raised in the checkpoint feedback. Key findings pending notebook execution: (1) if within-industry bimodality holds, company size / rater thresholds are the driver; (2) if UMAP shows cleaner grade clusters than PCA, non-linear modelling is justified; (3) ablation will confirm whether the 100-sentence cap is actively hurting S/G predictions.

**Next Steps**: Raise FinBERT sentence cap to 300+ and re-run feature extraction (M5.T6). Investigate non-semantic structured features such as market cap and revenue as supplements (M5.T12). Consider reframing as a binary classification problem (B/BB vs BBB/A) given the confirmed bimodal structure (M5.T13).

---

## 2026-03-07 - EDA on Filing Features, Feature Engineering, Modeling Iteration, Checkpoint Submission (Caden, Ethan, John)

**Context**: Baseline models from work.ipynb showed negative R² across all targets, indicating the raw 76-feature FinBERT set lacked the signal needed for reliable ESG score prediction. EDA on the feature dataset was needed to diagnose why and guide remediation before the checkpoint submission deadline.

**Work Completed**:
- Filled out `work/EDA_10-K_filings.ipynb` with full exploratory analysis of the 76 FinBERT-extracted features across 332 companies:
  - Identified 6 zero-variance features: `num_sections` (constant), `kw_employee_welfare_present` (100%), and 4 failed regex features (`has_women_leadership`, `count_women_leadership`, `has_board_independence`, `count_board_independence`)
  - Identified 5 near-universal binary presence flags (>95% of filings) that carry no discriminative signal
  - Identified extreme `pos_neg_ratio` outliers (max ~9,000,000) caused by near-zero negative means — a division artifact
  - Confirmed all 14 keyword frequency distributions are right-skewed, motivating log-transformation
  - Discovered 68–79% of companies hit the 100-sentence cap for Social and Governance pillars, creating a floor effect that limits S/G feature variance
  - FinBERT E_pillar_score shows the strongest correlation with ESG targets (r ≈ 0.38 with environment_score); Social and Governance features are near-random predictors
  - Industry is confirmed as the dominant driver of environment score variation — its absence from the original regression feature set was the primary modeling gap
- Applied all EDA recommendations to `work/work.ipynb` Step 7.5 Feature Engineering:
  - Dropped 11 features (6 zero-variance + 5 near-universal presence flags)
  - Clipped `E/S/G_pos_neg_ratio` at 99th percentile
  - Replaced 14 raw `kw_*_freq` columns with `log(x+1)` versions
  - Added 38 one-hot industry dummy variables → 103 total features
- Updated model training in `work/work.ipynb` (Steps 8 and 11):
  - Ridge alpha raised from 1 → 50; XGBoost hardened to `max_depth=3`, `reg_lambda=5`, `reg_alpha=1`, `subsample=0.8`
  - Added 5-fold cross-validation to both models; XGBoost CV R² improved to +0.054 (first positive signal)
  - Updated results: Ridge Test R² = −0.12 (from −0.27), XGBoost Test R² = −0.10 (from −0.14)
- Completed `checkpoint/submission.ipynb` with all rubric-required sections:
  - Title, Team, Overview (Heilmeier catechism answers)
  - Data section with provenance validation, score distribution plots, industry breakdown, correlation heatmap, PCA visualization
  - Preprocessing section covering all 5 steps (SPAC removal, industry standardization, 10-K extraction, FinBERT extraction, feature engineering)
  - Modeling section with regression results, per-pillar results, feature importance plot, grade classification baseline with confusion matrix
  - Problems & Challenges and Next Steps sections
- Updated `checkpoint/submission.ipynb` after modeling iteration to reflect engineered feature set, new results, and updated next steps

**Files Modified**:
- `work/EDA_10-K_filings.ipynb` (created full EDA)
- `work/work.ipynb` (added Step 7.5, updated Steps 8 and 11)
- `checkpoint/submission.ipynb` (all sections completed and updated)

**Impact**: M3.T5, M4.T3, M4.T4, M4.T5, M4.T6 complete. Baseline models established with improved feature engineering. Key finding: 10-K text features carry measurable but weak ESG signal (XGBoost CV R² = +0.054). The primary limitation is that 10-K filings are financial compliance documents, not ESG disclosures — most ESG-specific data lives in standalone sustainability reports.

**Next Steps**: Reframe regression target as industry-relative residuals (deviation from industry mean) to focus the model on company-level variation that text features can actually explain. Implement Lasso/ElasticNet with `LassoCV` for automatic feature selection. Raise FinBERT sentence cap to 300+ for Social/Governance.

---

## 2026-03-06 - FinBERT Feature Extraction Complete (Caden)

**Context**: With all 332 complete 10-K filings extracted and validated, the next step was to run FinBERT inference on ESG-relevant sentences to generate features for each company.

**Work Completed**:
- Ran FinBERT (ProsusAI/finbert) inference on ESG-relevant sentence subsets from all three extracted sections for all 332 companies with complete filings
- Processed ~3.5 hours on CPU using batched inference with no gradient computation
- Extracted 76 features per company across four groups:
  - 24 FinBERT sentiment features (pos/neg/neutral mean, std, sentence count, pos/neg ratio, composite pillar score) per E/S/G pillar
  - 28 ESG keyword frequency features (per-1000-word freq + binary presence for 14 topic groups)
  - 9 document structure features (section lengths and proportions, per-pillar coverage %)
  - 15 quantitative signal features (regex-detected presence and counts of emissions numbers, dollar fines, employee headcount, etc.)
- Merged FinBERT features with ESG labels (environment_score, social_score, governance_score, total_score, total_grade) from the cleaned ESG dataset
- Saved feature importance from initial XGBoost run

**Files Created**:
- `data/finbert_features/finbert_features.csv` (332 companies × 76 features)
- `data/finbert_features/merged_features_labels.csv` (332 companies × 83 columns including targets)
- `data/finbert_features/feature_importance.csv` (XGBoost feature importances)

**Impact**: M3.T4, M4.T1, M4.T2 complete. Full feature matrix ready for modeling. Initial XGBoost results showed negative R², motivating further EDA and feature engineering.

---

## 2026-03-04 - 10-K Section Extraction Finalized and Data Merged (Caden)

**Context**: Initial extraction run in early March produced more raw output than expected but required auditing to determine which companies had usable data for FinBERT feature generation.

**Work Completed**:
- Finalized and validated 10-K section extraction across all 709 companies:
  - 332 companies with complete 3/3 sections (Business, Risk Factors, MD&A)
  - 12 companies with partial extractions (1–2 sections) — excluded from modeling
  - 25 companies with no extractable data (missing files or no section matches)
- Ran extraction quality audit across all saved JSON files:
  - Business (Item 1): mean 67,600 chars, median 47,300 chars
  - Risk Factors (Item 1A): mean 123,100 chars, median 85,100 chars
  - MD&A (Item 7): mean 96,000 chars, median 64,300 chars
- Confirmed secondary-match extraction logic (skipping TOC occurrences) is functioning correctly; verified against MSFT, AAPL, and AMZN filings manually
- Merged extracted section metadata with ESG dataset to create the base modeling dataset

**Files Modified**:
- `work/10-K_filings_data_pull.ipynb` (extraction quality check step added)
- `data/extracted_sections/` (332 company JSON files with extracted text)

**Impact**: M3.T1, M3.T2, M3.T3 complete. 332-company modeling dataset is clean and validated. The 47% yield (332/709) reflects non-standard filing formats in the remainder — relaxing regex patterns is a future improvement.

---

## 2026-03-02 - ESG Dataset EDA Complete (Ethan)

**Context**: Need to extract business, risk, and MDA sections of 10-K filings for as many companies in the ESG dataset, so embeddings can be generated and initial FinBERT model can be completed before checkpoint is due.

**Work Completed**:
- Refined preprocessing and processing pipeline for 10-K key section extraction.
- Identified CIK as a better identification for 10-K file extraction than ticker.
- Created ESG_data_cleaned.csv, which is just the original dataset without companies with NA for industry.

**Impact**: M3.T1 in progress. Relevant sections have been extracted from 10-K filings for over 600 companies in the ESG dataset, need to audit the extractions to validate their sections for embedding generation.

---

## 2026-02-28 - ESG Dataset EDA Complete (Ethan)

**Context**: Need to understand the structure, quality, and distribution of the ESG ratings dataset before feeding it into the FinBERT model pipeline.

**Work Completed**:
- Performed full exploratory data analysis on the Kaggle ESG dataset (709 companies after cleaning)
- Identified and removed 13 SPAC companies with missing industry labels
- Standardized 4 inconsistent industry labels (formatting inconsistencies only)
- Analyzed score distributions, grade distributions, level distributions, and categorical breakdowns
- Performed correlation analysis and pairplot visualization across ESG pillars
- Conducted industry breakdowns including mean scores, boxplots, and pillar heatmap
- Performed temporal analysis confirming dataset is a single 2022 cross-sectional snapshot
- Conducted outlier detection using IQR method, identifying 12 outlier companies
- Verified score vs grade consistency, confirming bond-rating style hierarchy
- Confirmed 100% CIK coverage across all 709 companies

**Files Created**:
- `work/EDA_ESG.ipynb`

**Impact**: M2.T4 complete. ESG dataset is cleaned, understood, and ready for merging with 10-K filing text embeddings in the next pipeline stage.

---

## 2026-02-23 - Initial 10-K Filings Downloads/Extraction Functions Complete (Ethan)

**Context**: Need to extract relevant text data from 10-K filings for FinBERT model, but text files are very large so only necessary sections need to be extracted - business, mda, risks.

**Work Completed**:
- Downloaded 608/722 10-K filings from SEC EDGAR for the 2021-2022 period, with 114 failures due to delistings, acquisitions, and SPACs
- Built and verified extraction functions that correctly pull Item 1 (Business), Item 1A (Risk Factors), and Item 7 (MD&A) from each filing by skipping TOC matches and extracting until the next section boundary
- Updated Workplan tasks for upcoming and current Milestone goals

**Files Created**:
- `work/10-K_filings_data_pull.ipynb`
- `work/EDA_10-K_filings.ipynb`
- `data/sec-filings`
- `data/extracted_sections`

**Impact**: M2.T1 complete. 10-K filings downloaded, code in place to extract sections for key ESG text - business, mda, risk. Created work and data files for 10-k filings.


## 2026-02-03 - Project Planning and Setup (Team)

**Context**: First team meeting to set up project plans and create proposal.

**Work Completed**:
- Downloaded Kaggle ESG dataset and ensured SEC 10-K filing data can be retrieved through Python library
- Set up repository structure (admin/, work/, data/)
- (Ethan) Created initial VISION.md and WORKPLAN.md
- Completed project proposal

**Impact**: M1.T1, M1.T2, and M1.T4 complete. Project infrastructure in place, data is identified, future tasks are accounted for.
