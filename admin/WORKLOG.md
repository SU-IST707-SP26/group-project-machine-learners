# WORKLOG.md

## 2026-03-02 - ESG Dataset EDA Complete (Ethan)

**Context**: Need to extract business, risk, and MDA sections of 10-K filings for as many companies in the ESG dataset, so embeddings can be generated and initial FinBERT model can be completed before checkpoint is due.

**Work Completed**:
- Refined preprocessing and processing pipeline for 10-K key section extraction.
- Identified CIK as a better identification for 10-K file extraction than ticker.
- Created ESG_data_cleaned.csv, which is just the original dataset without companies with NA for industry.

**Impact**: M3.T1 in progress. Relevant sections have been extracted from 10-K filings for over 600 companies in the ESG dataset, need to audit the extractions to validate their sections for embedding generation.

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

## 2026-02-23 - Initial 10-K Filings Downloads/Extraction Functions Complete (Ethan)

**Context**: Need to extract relevant text data from 10-K filings for FinBERT model, but text files are very large so only necessary sections need to be extracted - business, mda, risks.

**Work Completed**:
- Downloaded 608/722 10-K filings from SEC EDGAR for the 2021-2022 period, with 114 failures due to delistings, acquisitions, and SPACs
- Built and verified extraction functions that correctly pull Item 1 (Business), Item 1A (Risk Factors), and Item 7 (MD&A) from each filing by skipping TOC matches and extracting until the next section boundary
- Updated Workplan tasks for upcoming anc current Milestone goals

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