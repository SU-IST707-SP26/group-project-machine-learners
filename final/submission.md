# Democratizing ESG: Predicting Corporate Sustainability Scores from Free Public Data

## Team

- Ethan Radecki (GitHub ID: 244611837)
- Caden Lippie (GitHub ID: 155611281)
- John Masseria (GitHub ID: 194737462)

## Introduction

Commercial ESG (Environmental, Social, and Governance) ratings from agencies like MSCI and Sustainalytics cost $5,000 to over $30,000 per year and cover roughly 10,000 companies worldwide. The methodologies behind these ratings are proprietary, which means that neither the investors paying for the data nor the companies being rated understand how their scores are calculated, this creates two problems. First, retail investors who want to align their portfolios with personal values are priced out of the data entirely, and second, most public companies remain unrated and invisible to the growing pool of ESG-focused capital. The global ESG asset market exceeds $30 trillion, and yet the majority of market participants lack access to the information needed to participate in it.

Our project addresses this gap by building a machine learning pipeline that predicts corporate ESG scores using only freely available public data. In the United States, all public companies must file annual 10-K filings with the Securities and Exchange Commission (SEC). These filings contain comprehensive business information including risk disclosures, management discussion, and financial statements. For this project, we chose to extract text from three key sections of these 10-K filings, generate financial sentiment and keyword features using FinBERT (a finance-specific language model), and supplement these with structured financial fundamentals pulled from the SEC EDGAR XBRL API. The combined feature set feeds into an ElasticNet regression model, with coefficients and SHAP values providing feature-level explanations for every prediction. The pipeline solely uses freely available public data, which means there's no proprietary feeds, no prior ESG ratings, and no cost barrier.

This project is a proof of concept rather than a production-ready system. Our best single-target result is a Social pillar R² of 0.173, and our models cannot predict Governance scores at all from the 10-K text and financial fundamentals alone, which is a finding that we frame as a structural ceiling confirming that governance scores require external data sources that do not appear in annual 10-K filings. These results are modest compared to prior research, but those studies typically relied on financial ratios or prior ESG scores as model features, which are either expensive or self-reinforcement mechanisms for the scores being predicted. Instead of providing a production-ready solution, our contribution is methodological, since we are able to demonstrate that free publicly available data can extract interpretable ESG signals and a transparent machine learning pipeline can partially replicate what commercial providers charge thousands of dollars a year for. For our primary stakeholders, which are retail investors, this establishes a foundation that future work could extend with additional data sources to improve predictive performance while maintaining the transparency and accessibility that commercial ratings lack.

## Literature Review

Environmental, social, and governance (ESG) ratings are quantitative assessments that professional agencies assign to companies based on their performance in environmental, social, and governance fields. The major agencies that assign these scores include MSCI, Sustainalytics, Refinitiv, and S&P Global. These agencies use proprietary methodologies to analyze publicly available data, such as annual company reports and sustainability disclosures[^1][^2]. Each agency evaluates a company's exposure to material ESG risks that are relevant to their industry, and then assesses the quality of the management systems the company has in place to address those risks. For example, MSCI covers over 10,000 companies across the globe and assigns relative ratings from AAA to CCC based on weighted assessments across 35 key ESG issues[^1]. Access to these ratings requires subscriptions that can range from $5,000 to over $30,000 annually, with higher level services demanding even higher fees[^3]. Despite their widespread adoption by asset managers and institutional investors, the specific calculations, weightings, and transformations that determine these ratings remain proprietary.
 
Despite ESG ratings widespread use in financial markets, the ratings themselves face significant limitations related to accessibility and reliability. A 2021 study completed by the CFA Institute found that correlations between major ESG ratings providers fall below 50%, suggesting that the same company can receive significantly different ratings depending on the evaluating agency[^4]. In contrast, credit ratings, another common practice in the financial industry, consist of correlations between agencies of over 94%. Aside from the major inconsistency, the proprietary nature of these methodologies creates a "black box" issue, where neither companies nor investors understand how these scores are calculated. Allianz Global Investors, a major asset manager, acknowledged these concerns as a critical limitation and developed its own ESG rating system to provide explainability and accountability[^5]. Additionally, the cost barriers create even more access problems. Subscriptions priced in the thousands of dollars per year effectively exclude retail investors from accessing ESG data, while the limited coverage means the vast majority of public companies remain unrated and invisible to ESG-focused investors.
 
Recent research shows that ML models can effectively predict ESG scores using various data sources. Multiple studies have shown that models incorporating financial data and textual analysis achieve accuracy scores of over 80%[^6][^7]. For example, a 2024 study using BERT-based NLP models achieved an ESG classification accuracy of 80.79%[^6], while another study reported an R² of 0.979 using stacked ensemble ML approaches[^7]. A key technology for this work has been FinBERT, a finance-specific language model that significantly outperforms general-purpose NLP models for financial text analysis tasks[^8]. FinBERT has been applied successfully to financial sentiment analysis and ESG classification tasks, emphasizing the viability of using NLP techniques to extract meaningful insights from corporate disclosures.

However, most existing research relies on either financial ratios or prior ESG ratings as primary predictors. The high performance figures from those studies reflect the strength of those features and signals they provide. Financial ratios capture company fundamentals that correlate with ESG investments and prior ESG ratings are somewhat of a self-reinforcement mechanism for the scores that are being predicted. There has been limited exploration of predicting ESG scores directly from raw text of regulatory filings without these additional features.

Our approach builds on the foundations laid above but departs from prior work in two different ways. First, for this project we made it a necessity that only freely available data was used, which led us to both the SEC 10-K filings and the EDGAR financial fundamentals, instead of proprietary data or prior ESG scores. These choices deliberately remove the cost barrier entirely and avoid the self-reinforcement issue with utilizing prior ESG scores for model predictions. Second, we paired FinBERT text features with ElasticNet regression rather than the ensemble methods common in prior studies. This decision was driven by a finding during model selection, which is that when you have 323 companies and 108 features the dataset will exhibit sparse signals with most features so many will carry little weight within the training process but still be included to make predictions. ElasticNet was the right choice to handle these sparse signals since its L1 regularization drives irrelevant features to exactly zero, which produced more stable cross-validated performance than XGBoost across all four ESG targets. The ElasticNet coefficients themselves are intuitive and useful for drawing actionable decisions from the model, but SHAP explainability was combined to further emphasize the importance of transparency and explainability so that stakeholders could understand which disclosure patterns and financial characteristics drive the predictions - educating their investment strategies.

Our primary stakeholders are retail investors, which are individual investors that manage their own portfolios and who increasingly want to align their investment strategies with personal values but cannot afford institutional ESG data subscriptions. A secondary stakeholder group consists of small and mid-cap public companies that currently lack ESG scores and need insight into how they perform relative to industry peers. By providing free, explainable ESG score predictions derived from solely publicly available data, this work addresses the accessibility and transparency gaps that existing commercial scores leave open.

## Data and Methods

### Data

<!-- TODO -->

### Methods

<!-- TODO -->

## Supporting Files

<!-- TODO: Index of supporting notebooks in work/ directory -->

## Results

<!-- TODO -->

## Discussion

<!-- TODO -->

## Limitations

<!-- TODO -->

## Future Work

<!-- TODO -->

## References

[^1]: MSCI, "MSCI ESG Ratings Methodology," 2024. https://www.msci.com/documents/1296102/34424357/MSCI+ESG+Ratings+Methodology.pdf

[^2]: Sustainalytics, "ESG Risk Ratings Methodology Abstract - Version 3.1," June 2024. https://www.sustainalytics.com/docs/knowledgehublibraries/default-document-library/sustainalytics_-esg-risk-ratings_-version-3-1_-methodology-abstract_-june-2024.pdf

[^3]: MSCI ESG Research LLC, "Form ADV Part 2A," March 2017. https://www.msci.com/documents/1296102/1311232/ESG+ADV+2A+2017-03.pdf

[^4]: CFA Institute, "ESG Ratings: Navigating Through the Haze," August 2021. https://blogs.cfainstitute.org/investor/2021/08/10/esg-ratings-navigating-through-the-haze/

[^5]: Allianz Global Investors, "ESG ratings - are they still relevant?" 2025. https://www.allianzgi.com/en/insights/outlook-and-commentary/sustainable-investing-esg-ratings-are-they-still-relevant

[^6]: PMC, "ESG2PreEM: Automated ESG grade assessment framework using pre-trained ensemble models," 2024. https://pmc.ncbi.nlm.nih.gov/articles/PMC10884917/

[^7]: MDPI Forecasting, "Predicting ESG Scores Using Machine Learning for Data-Driven Sustainable Investment," January 2025. https://www.mdpi.com/2813-2203/5/1/7

[^8]: Huang, A., Wang, H., & Yang, Y., "FinBERT: A Large Language Model for Extracting Information from Financial Text," Contemporary Accounting Research, January 2023. https://onlinelibrary.wiley.com/doi/10.1111/1911-3846.12832

[^9]: King, A., "Public Company ESG Ratings Dataset," Kaggle, March 2024. https://www.kaggle.com/datasets/alistairking/public-company-esg-ratings-dataset