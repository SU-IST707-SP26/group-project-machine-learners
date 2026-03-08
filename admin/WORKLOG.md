# WORKLOG.md

## 2026-03-07 - EDA on Filing Features, Feature Engineering, Modeling Iteration, Checkpoint Submission (Caden)

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
