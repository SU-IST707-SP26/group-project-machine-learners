# WORKPLAN.md

## Active Plan

### Milestone 1: Data Identification and Project Proposal
- [✅] M1.T1 — Find and Download Kaggle ESG dataset (Team)
- [✅] M1.T2 — Ensure SEC 10-K filing data can be retrieved through Python library (Ethan)
- [✅] M1.T4 — Complete and push proposal along with management docs (Ethan)

### Milestone 2: Data Verification and Quality Assessment
- [✅] M2.T1 — Collect SEC 10-K files for companies in ESG dataset, must filter for temporal variable (Ethan)
- [ ] M2.T2 — Verify target feature(s) from ESG dataset - number scores and class scores (X)
- [⏳] M2.T3 — Assess text quality and length distribution across 10-K sections to validate FinBERT input feasibility (Ethan) 
- [✅] M2.T4 — Complete EDA on ESG dataset, identify potential class/industry imbalances (Ethan)

### Milestone 3: Preprocessing Pipeline, Understand 10-K Structure, FinBERT Operational
- [ ] M3.T1 — Collect and extract Business, Risk Factors, and MD&A sections from 10-K filings for all companies in ESG dataset (X)
- [ ] M3.T2 — Audit extracted sections for quality, length distribution, and partial extractions (X)
- [ ] M3.T3 — Merge extracted 10-K data with ESG dataset on ticker symbol (X)
- [ ] M3.T4 — Complete first test of FinBERT model with extracted text data, verify embeddings generate correctly (X)

### Milestone 4: FinBERT Embeddings Generated, Train/Test Split, Logistic Regression Output
- [ ] M4.T1 — Generate FinBERT embeddings for all three sections across all companies (X)
- [ ] M4.T2 — Combine FinBERT embeddings with company metadata (industry classification) (X)
- [ ] M4.T3 — Perform stratified train/test split (X)
- [ ] M4.T4 — Train baseline logistic regression model and evaluate performance (X)

### Milestone 5: Multiple Models Trained/Compared and Best Model Identified
- [ ] M5.T1 — Train additional models (Random Forest, Gradient Boosting, XGBoost) (X)
- [ ] M5.T2 — Tune hyperparameters on top performing models (X)
- [ ] M5.T3 — Compare models using consistent evaluation metrics (RMSE, R², F1) (X)
- [ ] M5.T4 — Apply SHAP values to best model for explainability analysis (X)
- [ ] M5.T5 — Convert predicted scores to letter grades and document final model selection (X)


### Milestone 6: Final Model Validated, Evaluation, Explainability Framework
- [ ] M6.T1 — Validate final model on holdout test set and report evaluation metrics (X)
- [ ] M6.T2 — Run SHAP analysis and identify top features driving ESG score predictions (X)
- [ ] M6.T3 — Analyze model performance across industries and ESG pillars (X)
- [ ] M6.T4 — Document explainability findings and compare against commercial ESG rating methodology (X)

### Milestone 7: Visualizations, Final Report, Presentation
- [ ] M7.T1 — Build visualizations for model performance, SHAP values, and ESG score distributions (X)
- [ ] M7.T2 — Write final report covering methodology, results, and limitations (X)
- [ ] M7.T3 — Prepare and deliver final presentation (X)

---

## Changelog

### 2026-02-03
- (Ethan) ⏳ M1.T3 — SEC 10-K filing data must be filtered so it is from before the ESG data from Kaggle; Kaggle dataset from throughout 2022

### 2026-02-23
- (Ethan) ⏳ M2.T3 — 10-K filing data extraction for business, mda, and risk sections is computational intensive, may need to optimize operations or pipeline if computation with FinBERT because extreme as well.

### 2026-02-28
- (Ethan) ⏳ M2.T4 — ESG dataset EDA revealed environmental score bimodality and industry imbalance that will need to be addressed during feature engineering and model training.