# Medicare Beneficiary Churn Prediction

A data-driven machine learning system to predict county-level Medicare beneficiary churn using large-scale public healthcare data. This project was built during a data mining hackathon and achieved **1st place**.

---

## Problem Statement

CMS publishes monthly Medicare enrollment data across all US counties. The objective was to predict:

> Which counties will lose beneficiaries next month?

This enables proactive healthcare interventions such as targeted outreach and resource allocation.

This is a binary classification problem:

- churn = 1 → next month's beneficiaries decrease  
- churn = 0 → otherwise  

---
## Key Insight

![EDA Insight](images/eda_insight.png)

Most counties show growth overall, but a subset consistently declines.  
Decline is not random. It is driven by composition and recent momentum rather than population size.

## Structural Shift in Churn

![Churn Trend](images/churn_trend.png)

Churn rates increase significantly after 2019, indicating a structural change in beneficiary behavior rather than random fluctuation.

## Model Performance

![ROC Curve](images/roc_curve.png)

XGBoost achieved a ROC-AUC of 0.759, showing strong ranking ability across counties.

## What Drives Churn

![Feature Importance](images/feature_importance.png)

Recent enrollment changes and time-based signals dominate prediction, confirming that momentum matters more than static demographics.

## From Prediction to Action

![Resource Allocation](images/resource_allocation.png)

Predictions were converted into a prioritization strategy:

priority_score = churn_probability × population

This ensures resources are allocated where both risk and impact are highest.

## Risk Segmentation

![Risk Segmentation](images/risk_segmentation.png)

A small fraction of county-months fall into high risk, but they impact millions of beneficiaries.

This enables prioritizing interventions where both risk and population are highest.
---
## Dataset

- Source: data.gov (CMS Medicare Enrollment Data)
- Total records: **563,758 rows**
- Features: 60 columns
- Time range: **2013 to 2025**
- Granularity:
  - National
  - State
  - County (primary modeling level)

---

## Data Cleaning

- Replaced suppressed values (`*`) with NULL
- Converted all numeric columns from string to numeric
- Removed invalid county entries
- Created multiple dataset slices:
  - full dataset
  - county annual
  - county monthly (used for modeling)
  - state dataset
- Deduplicated records
- Validated data integrity:
  - TOT_BENES ≈ AGED + DISABLED (99.3% match)

---

## Key Insights from EDA

- Medicare grew **32% (2013–2025)**
- Medicare Advantage grew **139%**
- All states grew, but many counties declined
- County-level decline happens despite national growth
- COVID created a structural shift in churn patterns
- Raw counts had **>0.99 correlation** with total beneficiaries

Key conclusion:  
**Raw counts are useless for modeling. Ratios are critical.**

---

## Feature Engineering

Total features: **22**

### Ratio Features (11)
- aged_ratio
- disabled_ratio
- dual_ratio
- ma_ratio
- drug_ratio
- gender ratios
- age-based ratios

### Change Features (7)
- month-over-month percentage changes
- momentum indicators

### Time Features (2)
- month_num (seasonality)
- time_index (trend)

### Other Features (2)
- total change
- totals gap

---

## Modeling

### Target
Predict next month's churn using time-series data.

### Train/Test Split
- Train: 2013–2022
- Test: 2023–2025
- Time-based split (no leakage)

---

## Models and Performance

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|------|---------:|----------:|-------:|----:|--------:|
| XGBoost | 0.8059 | 0.5098 | 0.1154 | 0.1882 | 0.7594 |
| Stacking | 0.8030 | 0.4836 | 0.1537 | 0.2333 | 0.7544 |
| Random Forest | 0.6148 | 0.3125 | 0.8129 | 0.4514 | 0.7535 |
| Logistic Regression | 0.7999 | 0.4125 | 0.0619 | 0.1076 | 0.6790 |

### Best Model
**XGBoost (ROC-AUC = 0.7594)**

---

## Threshold Optimization

Default threshold (0.5):
- Recall: 11.5%

Optimized threshold (0.22):
- Recall: 70.8%
- F1 score improved significantly

Key insight:
Model ranking is strong, threshold tuning unlocks real value.

---

## Key Feature Importance

Top features:
- tot_benes_chg1 (momentum)
- month_num (seasonality)
- time_index (trend)
- percentage changes
- aging shifts

---

## Business Impact

### Risk Segmentation

- Low risk: 87K county-months
- Medium risk: 23K
- High risk: 2.3K

### Total Beneficiaries at Risk
**48.6 million**

---

## Resource Allocation Strategy

Allocated 1,000 nurse visits using:

priority_score = churn_probability × population

Top counties:
- Los Angeles County
- Cook County
- Miami-Dade
- San Diego

Direct real-world application:
Optimized healthcare resource deployment.

---

## Dashboard

Built interactive dashboards in Databricks:

### Page 1
- State-level beneficiary distribution
- Aged vs disabled comparison

### Page 2
- Churn risk segmentation
- High-risk counties
- Beneficiaries at risk

---

## Tech Stack

- Databricks (Serverless)
- PySpark
- XGBoost
- Scikit-learn
- Pandas
- Plotly / Seaborn
- Delta Tables

---

## Key Engineering Decisions

- Used monthly data (12x more samples)
- Used time-based split
- Removed raw count features
- Used median imputation (not zero fill)
- Dropped slow models due to runtime constraints
- Applied threshold tuning for deployment readiness

---

## Results

- ROC-AUC: **0.7594**
- Scalable prediction pipeline built
- Real-world healthcare application demonstrated

**1st Place in Hackathon**

---

## Repository Purpose

This project demonstrates the full lifecycle of a machine learning system:

- Data cleaning
- Feature engineering
- Model building
- Evaluation
- Business application

It showcases the ability to translate raw data into actionable insights and real-world decision-making systems.
