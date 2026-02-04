# Machine Learners IST 707 Project Proposal

## Transparent ESG Prediction: Machine Learning for Accessible Corporate Sustainability Assessment

### Team Members

**Point of Contact (POC):** Ethan Radecki (GitHubID: 244611837)

**Team Members:**
- Caden Lippie (GitHubID: "###")
- John Masseria (GitHubID: "###")
- Ethan Radecki (GitHubID: 244611837)

Ethan Radecki will manage the repository and serve as the primary contact for project submissions.

## Introduction

#### What are you trying to do?

We are building a machine learning system that predicts corporate ESG (Environmental, Social, and Governance) scores using only free public documents. In the United States, all public companies must file annual reports with the Securities and Exchange Commission (SEC). These reports, called 10-K filings, contain comprehensive business information including financial statements, risk factors, and management discussion. Commercial ESG ratings cost thousands to tens of thousands of dollars annually and cover over 10,000 companies globally, excluding retail investors, small asset managers, and most public companies from accessing this information. Our system makes ESG assessment free and accessible to anyone.

#### What is new in your approach and why do you think it will be successful?

1. ***Democratization:*** We use only free public data (SEC 10-K filings, sustainability reports) rather than expensive proprietary data feeds. This ensures our model works for anyone without financial barriers.

2. ***Explainability:*** We use interpretable machine learning techniques (SHAP values) to identify which specific document sections and language patterns drive each prediction. This transparency addresses the "black box" problem, which is when rating agencies don't explain how they calculate scores, and provides actionable feedback.

We expect success because: (1) companies increasingly include ESG-related information in public filings due to *investor demand*, (2) financial NLP models enable sophisticated document analysis, and (3) recent research demonstrates that machine learning models can achieve high accuracy in ESG prediction using textual and financial data.

#### Who cares? If you are successful, what difference will it make?

The global ESG asset market exceeds $30 trillion, yet most market participants lack access to ESG data:

1. ***Retail investors*** who want values-aligned portfolios cannot afford institutional data subscriptions.

2. ***Small and mid-cap companies*** lack ESG ratings and remain invisible to ESG-focused capital.

Our project democratizes ESG assessment, enabling informed investment decisions and helping thousands of companies access sustainable capital markets.

### Literature Review

Environmental, social, and governance (ESG) ratings are quantitative assessments that professional agencies assign to companies based on their performance in environmental, social, and governance fields. The major agencies that assign these scores include MSCI, Sustainalytics, Refinitiv, and S&P Global. These agencies use proprietary methodologies to analyze publicly available data, such as annual company reports and sustainability disclosures[^1][^2]. Each agency evaluates a company's exposure to material ESG risks that are relevant to their industry, and then assesses the quality of the management systems the company has in place to address those risks. For example, MSCI covers over 10,000 companies across the globe and assigns relative ratings from AAA to CCC based on weighted assessments across 35 key ESG issues[^1]. Access to these ratings requires subscriptions that can range from $5,000 to over $30,000 annually, with higher level services demanding even higher fees[^16]. Despite their widespread adoption by asset managers and institutional investors, the specific calculations, weightings, and transformations that determine these ratings remain proprietary.

Despite ESG ratings widespread use in financial markets, the ratings themselves face significant limitations related to accessibility and reliability. A 2021 study completed by the CFA Institute found that correlations between major ESG ratings providers fall below 50%, suggesting that the same company can receive significantly different ratings depending on the evaluating agency[^4]. In contrast, credit ratings, another common practice in the financial industry, consist of correlations between agencies of over 94%. Aside from the major inconsistency, the proprietary nature of these methodologies creates a "black box" issue, where neither companies nor investors understand how these scores are calculated. Allianz Global Investors, a major asset manager, acknowledged these concerns as a critical limitation and developed its own ESG rating system to provide explainability and accountability[^5]. Additionally, the cost barriers create even more access problems. Subscriptions priced in the thousands of dollars per year effectively exclude retail investors from accessing ESG data, while the limited coverage means the vast majority of public companies remain unrated and invisible to ESG-focused investors.

Recent research shows that ML models can effectively predict ESG scores using various data sources. Multiple studies have shown that models incorporating financial data and textual analysis achieve accuracy scores of over 80%[^9][^11]. For example, a 2024 study using BERT-based NLP models achieved an ESG classification accuracy of 80.79%[^9], while another study reported an R² of 0.979 using stacked ensemble ML approaches[^11]. A key technology for this work has been FinBERT. This is a finance-specific language model that significantly outperforms general-purpose NLP models for financial text analysis tasks[^13]. FinBERT has been applied successfully to financial sentiment analysis and ESG classification tasks, emphasizing the viability of using NLP techniques to extract meaningful insights from corporate disclosures. Now, most existing research focuses on either financial ratios or past ESG ratings as primary predictors, while there has been limited emphasis and exploration of textual data from SEC filings. Our approach builds on this foundation by combining FinBERT analysis of available 10-K filings with explainable ML methods, which addresses both accessibility and transparency gaps in current ESG assessment practices.

#### Stockholder Analysis

Our project primarily serves two key stakeholder groups. The first group is retail investors, or individual investors managing their own portfolios, who increasingly want to align their investments with their personal values. Despite this interest, most cannot afford institutional ESG data subscriptions that often cost thousands of dollars per year. As a result, these investors lack affordable access to transparent ESG ratings that are needed to make informed decisions.

The second stakeholder group consists of small and mid-cap public companies that currently lack ESG ratings altogether. These companies need clearer insight into how they perform relative to peers in their industry and where they can improve in order to attract the growing pool of ESG-focused capital. However, limited coverage by traditional rating agencies leaves many of them effectively invisible to sustainability-minded investors.

Our system addresses the needs of both groups by providing free ESG predictions for any public company. In addition, it uses explainable ML models to highlight which specific elements of corporate disclosures contribute to each rating. This approach enables investors to make more values-aligned investment decisions while giving companies actionable guidance on how to improve their ESG performance.

### Data and Methods

#### Data

We will utilize two primary data sources that connect through company identifiers. The first is the Public Company ESG Ratings Dataset available on Kaggle, which contains ESG ratings for 722 mid-to-large cap publicly traded companies[^18]. This dataset includes 21 columns with company identifiers, like ticker symbols, CIK numbers, and exchange listings, industry classifications for 48 unique sectors, and ESG performance metrics for each pillar along with overall ESG ratings. Additionally, each rating is provided in numerical, letter, and categorical formats. The ratings were last updated throughout 2022, with some observations being last updated in January and some later in the year. This dataset originates from established ESG rating providers and will serve as the labeled target variable for our prediction models.

The second dataset that we will be using consists of SEC 10-K annual filings for each of the 722 companies in the Kaggle ESG dataset. These filings will be obtained using the "sec-edgar-downloader" Python library, which provides free access to the SEC EDGAR database. The Kaggle ESG dataset contains CIK numbers as stated previously, which we will be able to use to retrieve the corresponding 10-K filings. Each of the filings will contain comprehensive textual information, including business descriptions, risk disclosures, legal proceedings, and more. To ensure temporal consistency, we will download 10-K filings from the same time period as the ESG ratings from the Kaggle ESG dataset. These filings will serve as the primary input for our FinBERT model, analyzing the text to generate embeddings that capture ESG-relevant semantic information.

#### Methods

Our modeling approach will combine natural language processing with supervised machine learning. The pipeline will consist of text preprocessing, feature extraction using FinBERT, model training, and evaluation with explainability analysis.

For preprocessing, we will extract and clean textual content from each 10-K filing by removing the HTML formatting and non-textual elements. After, we will divide documents into ESG-relevant sections, which include Risk Factors, Business Descriptions, and Management Discussion and Analysis.

For feature extraction, we will use FinBERT, which is a pre-trained financial language model, to generate embeddings that capture semantic meaning relevant to ESG concepts[^13]. These generated embeddings will be combined with company metadata, like industry classification, from the Kaggle ESG dataset.

For model training, we will implement and test multiple supervised learning techniques, like logistic regression, random forests, and gradient boosting. The supervised models will predict continuous numeric ESG ratings, which will then be converted to letter grades using industry-standard thresholds.

Finally, for evaluation we will use regression metrics (MAE, R²) to evaluate numeric predictions and classification metrics (accuracy, F1-score) to evaluate letter grade conversions. Alongside these metrics we will perform explainability analysis. We will apply SHapley Additive exPlanations (SHAP) values to identify which textual features contribute the most to each prediction that is made by the model, which will directly address stakeholder needs for transparency. Our models will be validated through k-fold cross-validation.

### Project Plan

Along with this project proposal, you will commit an **initial version of the project management documents**.  However, I would also like you to provide a rough timeline here for the project in the following form, broken out into two week periods.

Period|Activity|Milestone
|---|---|---|
|2/2 - 2/16|Proposal and Vision Finalized </br> Full Data Retreival of 10-K filings </br> Initial EDA on both datasets|All data collected and verified, specifically 10-K filings </br> Initial data quality assessment complete
|2/16 - 3/2|Text preprocessing pipeline development </br> Explore 10-K text structure </br> FinBERT setup| Preprocessing pipeline functional </br> Understanding of 10-K text structure </br> FinBERT operational on sample data
|3/2 - 3/16|FinBERT embedding generation for full dataset </br> Feature engineering and dataset preparation </br> Logistic Regression model implementation|FinBERT embeddings generated for all 722 companies </br> Train/Test split complete and Logistic Regression results output
|3/16 - 3/30|Random Forest/Gradient Boosting model implementation </br> Hyperparamter tuning </br> Model comparison and selection| Multiple models trained and compared </br> Best model identified
|3/30 - 4/13|SHAP explainability analysis implementation </br> K-fold cross-validation </br> Performance evaluation and results analysis|Final model validated </br> All evaluation metrics computed </br> Explainability framework complete
|4/13 - 4/27|Visualization creation </br> Final report writing </br> Presentation preparation| Final presentation delivered </br> All project deliverables complete

### Risks

#### Data Quality and Temporal Risk

- The Kaggle ESG dataset contains ratings from 2022, so we must filter the 10-K filings to be from the same time period for accurate temporal analysis. If historical filings are incomplete for certain companies in our Kaggle ESG dataset, it may shrink from 722. 

*Mitigation*: Filter 10-K filings to be from 2021/2022 and exclude companies with missing data if they should occur, 722 should be a fine enough buffer. Will reassess.

#### Dataset Size Risk

- For complex ML tasks, 722 observations may be insufficient and could lead to overfitting.

*Mitigation*: Use simpler ML models, like logistic regression and random forests, and leverage transfer learning through FinBERT. May need to apply regularization if overfitting occurs.

#### Computational Constraint Risk

- Generating embeddings for 722 companies using FinBERT and training multiple models could lead to computational strains.

*Mitigation*: Process the embeddings in batches and cache. If necessary, prioritize most ESG-relevant document information.

#### Poor Model Performance Risk

- 10-K text may lack sufficient ESG signals leading to poor model accuracy.

*Mitigation*: Assess baseline performance early on. If results are poor, feature engineering improvements may be needed.

# Grading

- Does your proposal include all of the above mentioned sections? [1 point]
- Have you clearly identifed your stakeholders and their needs [1 point]
- Have you answered the 5 Heilmeier questions mentioned effectively [1 points]
- Are your initial project management documents in place and do they follow the guidelines [1 point]
- Is you proposal plan feasible, and do you have sufficient detail to give me confidence that you will succeed? [1 point]

[^1]: MSCI, "MSCI ESG Ratings Methodology," 2024. https://www.msci.com/documents/1296102/34424357/MSCI+ESG+Ratings+Methodology.pdf

[^2]: Sustainalytics, "ESG Risk Ratings Methodology Abstract - Version 3.1," June 2024. https://www.sustainalytics.com/docs/knowledgehublibraries/default-document-library/sustainalytics_-esg-risk-ratings_-version-3-1_-methodology-abstract_-june-2024.pdf

[^4]: CFA Institute, "ESG Ratings: Navigating Through the Haze," August 2021. https://blogs.cfainstitute.org/investor/2021/08/10/esg-ratings-navigating-through-the-haze/

[^5]: Allianz Global Investors, "ESG ratings - are they still relevant?" 2025. https://www.allianzgi.com/en/insights/outlook-and-commentary/sustainable-investing-esg-ratings-are-they-still-relevant

[^9]: PMC, "ESG2PreEM: Automated ESG grade assessment framework using pre-trained ensemble models," 2024. https://pmc.ncbi.nlm.nih.gov/articles/PMC10884917/

[^11]: MDPI Forecasting, "Predicting ESG Scores Using Machine Learning for Data-Driven Sustainable Investment," January 2025. https://www.mdpi.com/2813-2203/5/1/7

[^13]: Huang, A., Wang, H., & Yang, Y., "FinBERT: A Large Language Model for Extracting Information from Financial Text," Contemporary Accounting Research, January 2023. https://onlinelibrary.wiley.com/doi/10.1111/1911-3846.12832

[^16]: MSCI ESG Research LLC, "Form ADV Part 2A," March 2017. https://www.msci.com/documents/1296102/1311232/ESG+ADV+2A+2017-03.pdf

[^18]: King, A., "Public Company ESG Ratings Dataset," Kaggle, March 2024. https://www.kaggle.com/datasets/alistairking/public-company-esg-ratings-dataset




