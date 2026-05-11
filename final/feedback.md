I really like your domain problem here and the basic approach. The transparency-and-interpretability framing — using ElasticNet specifically because its standardized coefficients are directly readable — is well-motivated and well-executed. The ablation discipline is also good: you ran LSA features, reported the negative result (test R^2 fell from +0.019 to −0.227).

That said, I think there's something important to flag about how the most interesting finding in your project gets framed, and an opportunity that you identified but didn't pursue - specifically, modal predictions are actually quite good (comparatively speaking).  I encouraged you to focus more attention here after your final presentation, but I think you could have gone farther than you did. 

## The central observation

In your methods section (and in `archive/mode_split_modeling.ipynb`) you say:

> "Oracle results using true mode labels showed genuine within-mode signal (environment R² = +0.74, total R² = +0.60)… However, the mode classifier achieved only 61.5% accuracy."

This is striking. With perfect mode information, Environment R² goes from your final 0.019 to **0.74**. Total goes from 0.062 to **0.60**. The bimodal structure isn't a nuisance to model around — it's the central feature of the ESG score distribution, and once you condition on it, the 10-K features predict surprisingly well. This reframes the whole project: the residual challenge isn't ESG prediction from text, it's mode classification. 

But two things didn't follow:

**1. You only ran the within-mode oracle for Environment and Total.** I had really wanted you to develop this further. Social and Governance within-mode aren't reported. Given that Social is the only target with positive holdout R² in your pooled model (0.215), within-mode Social might have been the strongest result in the project. Same for Governance, where your final pooled result is essentially zero — within-mode Governance might tell you whether the failure is "no signal in 10-Ks at all" or "no signal once you control for disclosure mode." That distinction matters for the structural-ceiling claim.

**2. You didn't iterate on the mode classifier.** A depth-3 DecisionTree at 61.5% accuracy was the only attempt I could see. You identified mode classification as the bottleneck and then mostly stopped working on it. There were natural next steps: XGBoost or RandomForest on the same features; a logistic regression baseline; engineered features specifically aimed at mode (industry × company-size interactions, sustainability-report-existence indicators, IFRS/EU-listing indicators, age-of-company, sector-specific disclosure norms); class-weighted classifiers; ensemble approaches. Any one of these might have closed the gap that the soft-mode-probability approach was used to address.

## On the "soft mode probability" compromise

When hard mode-split degraded performance, you pivoted to using the DecisionTree's predicted probability as a continuous feature. This is a reasonable retreat, but it doesn't solve the underlying problem; it just averages the misassignment cost across companies rather than concentrating it.

Your own results show this: `mode_prob_high` has the largest standardized coefficient in every target (≈60 for Total, ≈50 for Environment). The entire downstream ElasticNet is fundamentally riding on a 61.5%-accurate classifier output. When that classifier outputs 0.55 for a true-low-mode company, the regression assigns a moderately-high baseline that the within-mode features can't correct. The CV/test gap on Environment (0.311 -> 0.019) is exactly the pattern you'd expect from a model whose main feature is noisy.

The right framing for next time: if the dominant feature in your downstream regression is a weak classifier's output, fix the classifier first. Treating it as a feature postpones the problem rather than addressing it.

## Strengths

- **Genuine methodological diligence.** You ran multiple ablations (LSA, hard vs. soft mode-split, XGBoost vs. ElasticNet vs. Ridge, hyperparameter tuning) and reported negative results honestly. The LSA experiment in particular is a clean piece of negative evidence.
- **The oracle within-mode experiment is the most important finding in the project.** You ran the right experiment, it produced a clear result, and the diagnosis (mode classification is the bottleneck) is correct.
- **Interpretability-first framing is well-motivated.** Choosing ElasticNet specifically because standardized coefficients give you direct, human-readable explanations — versus reaching for SHAP or LIME on tree models — is a smart choice that aligns with the stakeholder goal of transparency.
- **Honest about Governance.** Reporting R² ≈ 0 for Governance and framing it as a structural data limitation (rather than a modeling problem) is the right scientific posture. - **Free-data-only constraint is taken seriously.** No proprietary feeds, no prior ESG scores as features; aligns really well with your stakeholder needs. 
- **Sentence-cap floor effect diagnosed and fixed.** Noticing that 68–79% of Social/Governance sentences hit the initial 100-sentence cap and raising it to 300 was a good observation and the right response. 
- **Quality audit of extraction.** Catching 9 critical bad-extraction companies (XBRL contamination, TOC stubs) and removing them rather than letting noise into the training set was a smart move.

## Weaknesses

- **The headline within-mode oracle result is hidden in an archive notebook.** Environment R^2 = 0.74 and Total R^2 = 0.60 with mode information should have been the centerpiece. 
- **You didn't iterate on the mode classifier despite identifying it as the bottleneck.** One depth-3 DecisionTree attempt is not exhausting the option. This is really where I had wanted you to focus more attention. 
- **Within-mode oracle wasn't run for Social or Governance.** This leaves the analysis incomplete. Within-mode Social would have either been your strongest result or shown that bimodality matters less for Social — either outcome is informative.
- **Soft mode probability doesn't solve the bimodality problem.** It just propagates the classifier's error continuously rather than discretely. Your CV/test gap on Environment is the symptom.
- **n=67 test set is small enough that all R^2 estimates are very noisy.** Bootstrap CIs on the test R² would have helped quantify how much of the Social -> Environment difference is real versus sampling variation.
- **38 industry dummies on n=256 training is close to overfitting.** Some sectors have very few companies in the training split, so their coefficients are mostly noise. Aggregating to GICS sectors (11) rather than sub-sectors might have been cleaner.
- **Total Score model is essentially redundant.** Total ≈ weighted sum of pillar scores. Training a separate ElasticNet on Total is fitting noise around what the pillar predictions would imply. A simple "predict each pillar, sum the predictions" baseline would have been worth comparing to.
- **"Governance is structurally data-limited" is partly true but...** Proxy statements (DEF 14A filings) are also freely available on EDGAR and contain the governance content you need — board composition, executive compensation, voting records.  This omission is not so problematic, but rather the statement that the data isn't available. 
- **The 47% extraction yield introduces selection bias.** Companies with non-standard 10-K formatting are systematically excluded, and that formatting probably correlates with company age, sector, and disclosure sophistication — all of which plausibly correlate with ESG outcomes. You acknowledge this but don't quantify or address it.

## Closing thoughts

The diagnosis you arrived at — within-mode prediction works, mode classification is the bottleneck — is the correct diagnosis.  However, this means that you core problem was actually mode prediction first, and then optimizing regression on a per category basis. Sightly more effort on the mode classifier and then on extending the within-mode analysis to all four targets, would have produced a substantially stronger project. 

Nonetheless, other aspects of the project were thoughtful and well executed. Solid work overall.

**Score: 27/30**


---

## Final Project Grade
| Assessment Item | Ethan Radecki | Caden Lippie | John Masseria |
|---|---|---|---|
| **Proposal (5 pts)** | 5 | 5 | 5 |
| **Midterm Report (10 pts)** | 10 | 10 | 10 |
| **Final Presentation (5 pts)** | 5 | 5 | 5 |
| **Final Report (30 pts)** | 27 | 27 | 27 |
| **Weekly Updates (30 pts)** | 30 | 30 | 25 |
| **Total (80 pts)** | **77** | **77** | **72** |
