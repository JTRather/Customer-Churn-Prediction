# Customer Churn Prediction & Retention Analytics Platform

A portfolio project that goes beyond "predict churn" to answer four business questions:
who's at risk, why, how much revenue is at stake, and what to do about it.

**Stack:** Python (pandas, scikit-learn, XGBoost, SHAP) · SQL (SQLite, portable to Postgres/MySQL) · Streamlit · HTML dashboard

## Results (this run, 20,000 synthetic customers)

- **Overall churn rate:** 21.1%
- **Best model:** Logistic Regression (class-weighted) — ROC-AUC 0.739, Recall 0.829
- **Top churn drivers (SHAP):** contract type >> payment method > support tickets > monthly charges
- **Revenue at risk (High + Critical tiers):** $4.41M annually, across 10,482 customers
- Monthly-contract customers churn at **32.2%** vs **5.1%** for two-year contracts — the strongest single lever in the data

Full numbers: `reports/model_comparison.csv`, `reports/churn_drivers.csv`, `reports/risk_tier_summary.csv`

## Pipeline

```
data/raw/customers.csv          synthetic dataset (20k customers, realistic churn logic)
        │
src/load_sql.py                 loads into SQLite (sql/schema.sql), runs business EDA queries
        │
src/train.py                    trains & compares Logistic Regression / Random Forest / XGBoost
        │
src/explain.py                  SHAP global + individual churn-driver explanations
        │
src/predict.py                  risk tiers + revenue-at-risk for every customer
        │
dashboard / app                 HTML dashboard (published) + Streamlit app (app/streamlit_app.py)
```

## Reproducing this project

```bash
pip install -r requirements.txt
python src/generate_data.py     # or swap in a real dataset with the same columns
python src/load_sql.py
python src/train.py
python src/explain.py
python src/predict.py
streamlit run app/streamlit_app.py
```

## Notes on the synthetic data

`generate_data.py` builds churn probability from a logistic function of contract type,
tenure, engagement, support tickets, and monthly charges, then samples actual churn from
that probability (so nothing is a hard-coded label). This means the "discoveries" in the
EDA and SHAP outputs weren't hand-picked — they're the model recovering the same real-world
patterns (monthly-contract churn, support-ticket churn, low-engagement churn) that
subscription businesses report. Swap in a real dataset with the same column names and the
whole pipeline runs unchanged.

## What's *not* included (optional next steps)

- **Power BI (.pbix):** this environment can't produce a `.pbix` file, so the dashboard is
  built as a standalone HTML page (`dashboard/churn_dashboard.html`) with the same three
  pages (Executive Overview, Customer Risk, Churn Drivers) described in the original plan.
  It's a drop-in substitute — the same joined table (`customer_master.csv`) would load
  directly into Power BI if you have a license.
- **AWS deployment** (Phase 6) — infra work outside a portfolio-code scope; the Streamlit
  app is deploy-ready as-is on Streamlit Community Cloud or an EC2/ECS box.

## Resume bullet (fill in your own final numbers if you swap in real data)

> **Customer Churn Prediction & Retention Analytics Platform** | Python, SQL, Scikit-learn, Streamlit, SHAP
> - Analyzed 20K customer records using Python and SQL to identify behavioral and subscription
>   characteristics associated with churn, finding monthly-contract customers churn 6x more than
>   two-year customers.
> - Built and compared logistic regression, random forest, and XGBoost models, selecting a
>   class-weighted logistic regression at 0.74 ROC-AUC / 0.83 recall.
> - Built an interactive dashboard identifying high-risk customers and quantifying $4.4M in
>   estimated annual recurring revenue at risk.
> - Applied SHAP explainability to surface individual and global churn drivers, translating
>   model output into four actionable retention segments.
