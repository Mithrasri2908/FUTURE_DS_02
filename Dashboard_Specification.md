# Excel Dashboard Specification

**Customer Retention & Churn Dashboard**  |  Future Interns – Data Science & Analytics Task 2 (2026)  |  Prepared by Mithrasri P

**Workbook:** `Churn_Dashboard.xlsx`  |  **Audience:** founder, product manager, customer-success lead  |  **Purpose:** monitor churn, find where it concentrates, and decide where to act.

---

## 1. Workbook structure

| Sheet | Role |
|---|---|
| **Dashboard** | The only sheet most readers need: filters, KPI cards, 8 visuals, insight panel |
| **About** | Usage notes, data cleaning log, risk score definition, method notes |
| **Calc** | Formula tables feeding each chart (all respond to the filters) |
| **Cohort_Calc** | Kaplan–Meier retention by cohort (full dataset) |
| **Data** | 7,043 cleaned customer rows plus 5 formula-driven engineered columns |

Engineered columns in **Data**: `TenureGroup`, `ChargeBand`, `ChurnFlag`, `RiskScore`, `RiskTier`.

## 2. Filters (interactive)

Five dropdowns in row 6 of the Dashboard. Each defaults to **All**; every KPI and chart (except the cohort heatmap) recalculates when one changes.

| Filter | Options |
|---|---|
| Contract Type | All, Month-to-month, One year, Two year |
| Internet Service | All, DSL, Fiber optic, No |
| Gender | All, Female, Male |
| Senior Citizen | All, Yes, No |
| Payment Method | All, Bank transfer (automatic), Credit card (automatic), Electronic check, Mailed check |

Mechanism: `Calc!B2:B6` convert each choice into a COUNTIFS criterion (`"*"` when All). Every metric is a `COUNTIFS` / `AVERAGEIFS` / `SUMIFS` over `Data` using those five criteria.

**Why dropdowns instead of slicers?** Native slicers need an Excel Table or PivotTable connection that cannot be generated programmatically. Section 7 explains how to add true slicers in two minutes if preferred.

## 3. KPI cards

| KPI | Definition | Formula (Calc sheet) | Full-data value |
|---|---|---|---|
| Total Customers | Customers in current filter | `COUNTIFS(filters)` | 7,043 |
| Churned Customers | Customers with Churn = Yes | `COUNTIFS(filters, Churn, "Yes")` | 1,869 |
| Retained Customers | Total − churned | `=B9-B10` | 5,174 |
| Churn Rate | Churned ÷ total | `=IFERROR(B10/B9,0)` | 26.5% |
| Retention Rate | Retained ÷ total | `=IFERROR(B11/B9,0)` | 73.5% |
| Average Tenure | Mean months with company | `AVERAGEIFS(tenure, filters)` | 32.4 months |
| Average Monthly Charges | Mean monthly bill | `AVERAGEIFS(MonthlyCharges, filters)` | $64.76 |

Colour logic: red for churn metrics, teal for retention metrics, navy for neutral volumes.

## 4. Visualisations

| # | Visual | Chart type | Data and logic | What it tells the reader |
|---|---|---|---|---|
| 1 | **Churn Distribution** | Doughnut | Retained vs churned counts | Scale of the problem: 26.5% churned |
| 2 | **Churn by Contract Type** | Column | Churn rate per contract | Month-to-month 42.7% vs two-year 2.8% |
| 3 | **Churn by Internet Service** | Column | Churn rate per service | Fiber 41.9% vs DSL 19.0% vs none 7.4% |
| 4 | **Monthly Charges vs Churn** | Column (amber) | Churn rate by charge band (Under $35 to $95+) | Churn peaks at 36.3% in the $75–95 band |
| 5 | **Tenure vs Churn** | Column (navy) | Churn rate by tenure band (0–6 to 61–72 months) | Churn falls from 52.9% to 6.6% as tenure grows |
| 6 | **Customer Segmentation** | Column (red / amber / teal) | Customers per risk tier | 2,498 High, 1,805 Medium, 2,740 Low |
| 7 | **Retention Trend** | Line | Kaplan–Meier retention, months 0–72 | Steepest drop is in the first months; 59% remain at month 72 |
| 8 | **Cohort Heatmap** | Table with 3-colour scale | Retention at M1…M72 for 9 cohorts | Month-to-month decays to 59% by M24; two-year stays at 100% |

Charts 1–7 respond to the filters; the heatmap is deliberately fixed to the full dataset so cohorts stay comparable.

**Risk tier rule** (transparent points score, 0–16):

| Signal | Points |
|---|---|
| Month-to-month contract / one-year contract | +4 / +1 |
| Tenure ≤ 12 months / 13–24 months | +3 / +1 |
| Fiber optic | +2 |
| Electronic check | +2 |
| No Tech Support (internet customers) | +1 |
| No Online Security (internet customers) | +1 |
| Senior citizen | +1 |
| No partner and no dependents | +1 |
| Paperless billing | +1 |

Tiers: **High** 10+, **Medium** 6–9, **Low** 0–5. Observed churn: 55.0% / 20.6% / 4.5%.

## 5. Dashboard layout

Landscape, 21 narrow columns, gridlines hidden, light-grey canvas with white cards. Prints on two landscape pages.

```
┌───────────────────────────────────────────────────────────────────┐
│ TITLE BANNER (navy)                                               │  rows 1-3
├───────────────────────────────────────────────────────────────────┤
│ [Contract] [Internet] [Gender] [Senior] [Payment]   <- filters    │  rows 5-6
├───────────────────────────────────────────────────────────────────┤
│ KPI: Total | Churned | Retained | Churn% | Retention% | Tenure | $│  rows 8-11
├───────────────────────────────────────────────────────────────────┤
│ 1 | WHO LEAVES?                                                   │  row 13
│ [Churn Distribution] [Churn by Contract] [Churn by Internet]      │  rows 14-29
├───────────────────────────────────────────────────────────────────┤
│ 2 | PRICE, TENURE AND RISK                                        │  row 30
│ [Charges vs Churn]   [Tenure vs Churn]   [Risk Segmentation]      │  rows 31-46
├───────────────────────────────────────────────────────────────────┤
│ 3 | RETENTION TREND AND KEY INSIGHTS            (page 2)          │  row 47
│ [ Retention trend line (wide) ............ ] [Headline insights]  │  rows 48-63
├───────────────────────────────────────────────────────────────────┤
│ 4 | COHORT RETENTION HEATMAP (9 cohorts x 11 checkpoints)         │  rows 65-78
└───────────────────────────────────────────────────────────────────┘
```

Design principles: filters first, headline numbers second, "who leaves" before "why", one idea per chart, consistent colour meaning (red = churn or risk, teal = retention, navy = neutral), and a plain-English insight panel so a non-analyst can read the takeaway without interpreting charts.

## 6. Formatting standards

| Element | Spec |
|---|---|
| Font | Arial throughout |
| Palette | Navy `#1F3A5F`, red `#D1495B`, teal `#2A9D8F`, amber `#EDAE49`, canvas `#F4F6F9` |
| Percentages | `0.0%` (stored as fractions) |
| Currency | `$#,##0.00` for charges, `$#,##0` for revenue |
| Heatmap scale | Red at 10%, pale yellow at 60%, teal at 100% |

## 7. Optional: PivotTable + true slicers

1. On **Data**, select any cell and choose **Insert → Table** (name it `tblCustomers`).
2. **Insert → PivotTable** from `tblCustomers` on a new sheet. Rows: `Contract`; Values: Count of `customerID` and Average of `ChurnFlag` (format as %). That Average is the churn rate.
3. With the PivotTable selected: **PivotTable Analyze → Insert Slicer** and tick `Contract`, `InternetService`, `gender`, `SeniorCitizen`, `PaymentMethod`.
4. **Slicer → Report Connections** and connect each slicer to every PivotTable (one per chart).
5. Insert PivotCharts from each PivotTable and arrange them using the layout in section 5.

## 8. Data notes

- 11 blank `TotalCharges` values all belong to tenure-0 customers and were set to 0.
- `SeniorCitizen` recoded from 0/1 to No/Yes so filters read naturally.
- Charge band `Under $35` is spelled out because a leading `<` is read as a comparison operator by `COUNTIFS`.
- Retention uses Kaplan–Meier: churned customers are events, active customers are censored at their tenure. Contract and service values are current values and may have changed over a customer's life.
- Verified: the dashboard's unfiltered KPIs and segment tables match the independent pandas analysis exactly.
