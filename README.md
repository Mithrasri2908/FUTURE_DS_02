# Customer Retention & Churn Analysis

**Future Interns – Data Science & Analytics Task 2 (2026)**  |  Prepared by **Mithrasri P**

> A consulting-style analysis of a 7,043-customer subscription base: why customers leave, who is most at risk, what keeps them, and how to protect recurring revenue.

![Python](https://img.shields.io/badge/Python-pandas%20%7C%20scikit--learn-3776AB?logo=python&logoColor=white)
![Excel](https://img.shields.io/badge/Excel-Interactive%20Dashboard-217346?logo=microsoftexcel&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-2A9D8F)

---

## Table of Contents
1. [Project Overview](#project-overview)
2. [Business Problem](#business-problem)
3. [Dataset Information](#dataset-information)
4. [Tools Used](#tools-used)
5. [Methodology](#methodology)
6. [Key Insights](#key-insights)
7. [Dashboard Features](#dashboard-features)
8. [Results](#results)
9. [Business Recommendations](#business-recommendations)
10. [Folder Structure](#folder-structure)
11. [How to Reproduce](#how-to-reproduce)
12. [Limitations](#limitations)
13. [Future Improvements](#future-improvements)

---

## Project Overview

This project treats a public telecom customer dataset as the subscription business of a SaaS startup (recurring monthly fees, add-on modules, contract tiers, voluntary cancellation) and answers four questions a founder or product manager would ask:

| Question | Where it is answered |
|---|---|
| Why are customers leaving? | Churn analysis, EDA |
| Which customers are most likely to churn? | Risk segmentation (High / Medium / Low) |
| What factors improve retention? | Retention drivers, cohort analysis |
| How can the company reduce churn and grow lifetime value? | 12 prioritised recommendations with impact estimates |

**Deliverables:** a 27-page business report, an interactive Excel dashboard, a reproducible Python script, and portfolio assets.

## Business Problem

**1 in 4 customers is leaving.** 1,869 of 7,043 customers (26.5%) have churned, removing **$139,131 of monthly recurring revenue (30.5% of the total, about $1.67M a year)**. Churned customers also paid more than average ($74.44 vs $61.27 per month), so the revenue damage is larger than the headcount suggests.

## Dataset Information

| Item | Detail |
|---|---|
| Source | Telco Customer Churn (IBM sample data, available on Kaggle) |
| Records | 7,043 customers |
| Features | 21 original, 26 after feature engineering |
| Target | `Churn` (Yes / No), 26.5% positive |
| Groups | Demographics, tenure, services and add-ons, contract and billing, monthly and total charges |

**Cleaning highlights:** `TotalCharges` was stored as text with 11 blank values; all 11 belong to brand-new customers with tenure 0, so they were set to 0 rather than dropped. No duplicates, no other missing data. Full log in the report (Section 3).

## Tools Used

- **Python:** pandas, NumPy, scikit-learn, matplotlib (analysis, survival curves, charts, benchmark model)
- **Excel:** formula-driven dashboard (COUNTIFS / AVERAGEIFS / SUMIFS), dropdown filters, native charts, conditional-format heatmap
- **Word / Markdown:** business report and documentation

## Methodology

1. **Clean and validate**: type fixes, null and duplicate checks, category consistency checks.
2. **Engineer features**: tenure bands, charge bands, churn flag, risk score and tier.
3. **Explore**: churn rate by every demographic, contract, billing and service attribute.
4. **Retention analysis**: Kaplan–Meier survival curves, with active customers treated as censored, to measure retention decay without bias.
5. **Cohort analysis**: nine behavioural cohorts (contract, internet service, payment method) followed from month 1 to month 72.
6. **Risk segmentation**: a transparent 0–16 points score (rules a retention manager can read and tune), validated against a cross-validated logistic regression.
7. **Recommend and size**: each recommendation is quantified with a conservative rule (only half of the observed churn gap is assumed causal).

## Key Insights

| # | Insight | Evidence |
|---|---|---|
| 1 | **Contract length is the #1 driver** | Month-to-month churn 42.7% vs 2.8% on two-year plans; month-to-month is 55% of customers but **88.6% of churn** |
| 2 | **Churn is front-loaded** | 55.5% of churners leave in year one; monthly churn probability is 3.0% in months 1–3 vs about 0.5% after month 12 |
| 3 | **Fiber is the biggest revenue leak** | 41.9% churn, 69.4% of all churn, yet worth about $1,765 per customer over 24 months (DSL: $1,227) |
| 4 | **Payment method signals risk** | Electronic check churns at 45.3% vs 15–19% for other methods and holds 57.3% of churn |
| 5 | **Add-ons go with loyalty** | Internet customers with none of four protection add-ons churn at 56.7%; with all four, 5.3% |
| 6 | **Households stick** | Customers with no partner and no dependents churn at 34.2% vs 19.8% for others |
| 7 | **Gender does not matter** | 26.9% vs 26.2% |

![Churn by contract](images/churn_contract.png)

![Retention curves](images/retention_curves.png)

![Cohort heatmap](images/cohort_heatmap.png)

## Dashboard Features

The Excel dashboard (`dashboard/Churn_Dashboard.xlsx`) recalculates every KPI and chart from the customer-level data sheet (37,500+ live formulas, zero errors).

- **7 KPI cards:** Total Customers, Churned, Retained, Churn Rate, Retention Rate, Average Tenure, Average Monthly Charges
- **8 visuals:** Churn Distribution, Churn by Contract, Churn by Internet Service, Monthly Charges vs Churn, Tenure vs Churn, Customer (Risk) Segmentation, Retention Trend, Cohort Heatmap
- **5 interactive filters:** Contract Type, Internet Service, Gender, Senior Citizen, Payment Method
- **Transparent model:** engineered columns (tenure band, charge band, risk score, risk tier) are formulas, not pasted values

See [`dashboard/Dashboard_Specification.md`](dashboard/Dashboard_Specification.md) for layout, definitions and the PivotTable + slicer version.

## Results

| Risk tier | Score | Customers | Actual churn rate | Share of all churners |
|---|---|---|---|---|
| **High** | 10–16 | 2,498 (35.5%) | **55.0%** | **73.5%** |
| Medium | 6–9 | 1,805 (25.6%) | 20.6% | 19.9% |
| Low | 0–5 | 2,740 (38.9%) | 4.5% | 6.6% |

- Risk score AUC: **0.838** (cross-validated logistic regression benchmark: 0.843)
- **1,124 active customers** are High risk, holding **$82.7K of MRR**: the immediate save-desk list
- Illustrative impact of the quantified initiatives (30% overlap haircut): about **365 customers and $25.8K MRR (≈ $310K annualised) retained**, lowering churn from 26.5% to about 21.4%. These are planning estimates to be validated by A/B tests.

## Business Recommendations

| # | Recommendation | Why |
|---|---|---|
| 1 | Move month-to-month customers to annual plans | 88.6% of churn; $389 more revenue per customer over 24 months |
| 2 | Build a 90-day onboarding programme | Months 1–3 hold 31.9% of all churn |
| 3 | Migrate electronic-check payers to autopay | 45.3% churn vs 15–19% |
| 4 | "Fiber Protect": bundle Security + Tech Support | 55.0% churn without vs 14.2% with both |
| 5 | Fiber service and value review | 69.4% of churn sits in the highest-value product |
| 6 | Weekly save desk from the risk score | 1,124 active High-risk customers |
| 7 | Senior-friendly plan and assisted support | 41.7% churn |
| 8 | Household and family plans | Single households churn at 34.2% |
| 9 | Review the $75–95 price band | 36.3% churn, 35.5% of churn |
| 10 | Proactive one-year renewal journey | Retention drops from 92% to 83% between months 48 and 60 |
| 11 | Test billing and communication experience | Paperless churn 33.6% vs 16.3% (correlation, to be tested) |
| 12 | Build the retention measurement system | Capture reasons, tickets, NPS; A/B test every offer |

Full problem / recommendation / impact / business-value write-ups are in [`reports/`](reports/Customer_Retention_Churn_Report.docx).

## Folder Structure

```
churn-analysis-project/
├── README.md
├── data/
│   ├── raw/
│   │   └── WA_Fn-UseC_-Telco-Customer-Churn.csv
│   └── processed/
│       ├── telco_churn_cleaned.csv        # cleaned data + engineered features
│       └── retention_by_cohort.csv        # Kaplan-Meier retention table
├── scripts/
│   └── churn_analysis.py                  # reproduces metrics, cohorts, risk score
├── dashboard/
│   ├── Churn_Dashboard.xlsx               # interactive Excel dashboard
│   └── Dashboard_Specification.md         # KPI definitions, layout, slicer guide
├── reports/
│   └── Customer_Retention_Churn_Report.docx
├── images/                                # charts used in report and README
└── portfolio/
    ├── linkedin_post.md
    └── resume_entry.md
```

## How to Reproduce

```bash
pip install pandas numpy scikit-learn matplotlib
python scripts/churn_analysis.py
```

Open `dashboard/Churn_Dashboard.xlsx` in Excel, go to the **Dashboard** sheet and use the dropdowns in row 6.

## Limitations

- Single snapshot: no sign-up dates, so retention uses survival estimates by behavioural cohort rather than calendar-vintage cohorts.
- Associations are not proof of cause; impact estimates assume half of each observed gap is causal.
- The dataset is a public sample; conclusions should be re-validated on the company's own data.
- The risk score is in-sample and should be back-tested before it drives budget.

## Future Improvements

- Add a gradient-boosted model with SHAP explanations and compare it with the rule-based score on a holdout set.
- Collect sign-up date, cancellation reason, support tickets and NPS to enable true cohort tables and root-cause analysis.
- Add customer lifetime value and payback modelling to size retention spend per segment.
- Build the dashboard in Power BI or Tableau with automated monthly refresh.
- Design and run A/B tests for the top three retention offers, with holdout groups.

---

**Author:** Mithrasri P  |  Data Analyst Intern, Future Interns (2026)
