# Fraud Detection: A Product-First Case Study

**Try the interactive dashboard →** [Fraud Detection Dashboard](fraud_dashboard_canvas.html) *(hosted via GitHub Pages once enabled — see below)*

---

## The problem

A fraud model that catches more fraud almost always flags more innocent customers too. Every fraud team faces the same real decision: **where do you set the line?** Too loose, and legitimate customers get declined, challenged, and annoyed. Too strict, and real fraud slips through.

This isn't a modeling question — it's a business tradeoff. This project treats it as one.

## Who's affected

| Stakeholder | Wants |
|---|---|
| Fraud/Risk team | Minimize dollar losses |
| Customer Experience | Minimize false declines and friction |
| Legal/Compliance | Explainable, auditable decisions |
| Cardholder | Convenience, trust, no wrongful blocks |

## The decision

At what model confidence threshold should a transaction be auto-approved, challenged (step-up authentication), or blocked — and what does each choice cost, in real dollars?

## What we found

Using 1.3M+ real transaction records ([kartik2112 Fraud Detection Dataset](https://www.kaggle.com/datasets/kartik2112/fraud-detection), Kaggle):

- Fraud here isn't small "card-testing" transactions — it's **large, one-off anomalies** relative to a cardholder's own normal spending (avg. fraud amount $531 vs. $68 legit)
- A tuned XGBoost model reaches **94.7% fraud recall at ~44–70% precision**, substantially outperforming a logistic regression baseline (PR-AUC 0.95 vs 0.37)
- Translating that into dollars: **net benefit peaks around a 0.8 confidence threshold** — catching the large majority of fraud while cutting customer friction costs by roughly 70% versus a looser policy
- Geolocation/"impossible travel" signals — a common assumption in fraud modeling — showed **no predictive value in this dataset**, an honest finding that shaped which features made the final model

## Recommendation

Deploy at **threshold ≈ 0.8**: catches ~94.8% of fraud, keeps false positives to a level with the best measured net dollar benefit. Roll out via canary (5% → 25% → 100% of traffic), with a human-review queue for borderline cases in the first weeks and clear rollback criteria if false-decline complaints spike.

## What's in this repo

- **[`fraud_detection_analysis.ipynb`](fraud_detection_analysis.ipynb)** — full analysis: EDA, feature engineering, a real data-leakage catch-and-fix, model comparison, threshold/cost tradeoff, SHAP explainability
- **[`fraud_dashboard_canvas.html`](fraud_dashboard_canvas.html)** — interactive dashboard: drag the threshold slider and watch the ROC and PR curves respond, using this project's real model results
- **`shap_global_importance.png`** — exported static SHAP feature importance chart

## Data

The raw CSVs aren't included in this repo (500MB+, and Kaggle's terms prefer direct download). To reproduce:
1. Download [the dataset from Kaggle](https://www.kaggle.com/datasets/kartik2112/fraud-detection)
2. Place `fraudTrain.csv` in `data/`
3. Run the notebook top to bottom

## Honest limitations

- Dataset is 2019–2020 and partly simulated — real production fraud evolves faster than any static dataset shows
- The $25-per-false-positive friction cost is a simplifying assumption (no real churn/complaint data available) — flagged explicitly rather than presented as precise
- No demographic fields exist in this dataset to run a genuine fairness/bias audit — a real deployment would need one before shipping segment-specific thresholds
- Threshold is global (same cutoff for every cardholder) in this version — a reasonable next step, not covered here

## Setup

```bash
pip install -r requirements.txt
jupyter notebook fraud_detection_analysis.ipynb
```
