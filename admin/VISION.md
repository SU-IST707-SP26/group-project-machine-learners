# VISION.md

## Current Vision

### Project: Democratizing ESG - Transparent Corporate Sustainability Assessment

**Primary Stakeholder:** 
- Retail investors (individual investors managing their own portfolios) who want to align investments with personal values but cannot afford expensive ESG data subscriptions

**Secondary Stakeholder:** 
- Small and mid-cap public companies seeking to understand their ESG standing and attract sustainable investment capital

**Problem Statement:**
The global ESG investment market has surpassed $30 trillion, yet ESG ratings remain inaccessible to most market participants. Commercial ESG ratings cost $5,000-$30,000+ annually and cover only approximately 10,000 companies globally, excluding retail investors and leaving most public companies unrated. Additionally, existing ratings operate as "black boxes" where neither investors nor companies understand how scores are calculated, undermining trust and preventing actionable improvement.

**Solution:**
We will build a machine learning system that predicts corporate ESG scores using only freely available SEC 10-K filings. The system will use FinBERT, a financial natural language processing model, to analyze textual content from annual reports and generate ESG predictions. Critically, we will integrate SHAP (SHapley Additive exPlanations) values to provide explainability, showing which specific document sections and language patterns drive each prediction. This addresses both the accessibility barrier (free predictions for any public company) and the transparency gap (clear explanations of rating rationale).

---