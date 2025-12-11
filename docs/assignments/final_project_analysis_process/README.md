# Eviction Predictor: Philadelphia Eviction Activity Prediction Model Based on Macroeconomic and Financial Health

This project integrates multi-source datasets (Eviction Lab, BLS, Urban Institute) and employs an OLS linear regression model to forecast fluctuations in Philadelphia's monthly eviction petition activity relative to its historical baseline (`percentage_diff`).

## Project Setup, Data, and Objectives

### 1. Technical Environment
* **Language/Format:** R language, R Markdown (Rmd), Quarto.
* **Key R Packages:** `tidyverse`, `sf` / `tigris`, `ggplot2`, `broom`.

### 2. Data Integration
The project integrates nine datasets, enabling comprehensive analysis at both spatial (census tract) and temporal (monthly) scales.
* **Eviction Data:** Census Area Application Volume/Rate (`tract_filings`), Citywide Monthly Trends (`eviction_trends`).
* **Macroeconomic Data:** Monthly unemployment rate (`unemployment_monthly`), Consumer Price Index (CPI).
* **Financial Health Data:** Urban Institute Financial well-being indicators for residents.

### 3. Selection of Dependent Variable (`percentage_diff`)
* **Definition:** Monthly eviction application volume as a percentage deviation relative to the long-term historical baseline.
* **Advantages:** This variable is better suited to capture the marginal impact of short-term economic shocks on eviction pressures.

## Detailed Analysis and Key Findings

### A. Geospatial and Temporal Trend Analysis

#### 1. Geographic Concentration
* **Hotspots:** Eviction petitions are highly concentrated in **North Philadelphia** and **West Philadelphia**, areas where high-risk patterns reflect structural inequalities. 

#### 2. Time Series Trends
* **Trend:** After a sharp suppression in volume during 2020-2021 (due to COVID-19 stay orders), the volume began recovery in 2022 and stabilized in 2023-2024 (averaging approximately 1,060 applications per month).

### B. OLS Regression Model Results and Policy Implications

#### 1. Model Explanatory Power
* **Adjusted $R^2$:** **$0.7773$** (77.73%). The model exhibits strong explanatory power for monthly eviction fluctuations.

#### 2. Core Predictor Analysis

| Predictor | Estimate (Coefficient) | P-value | Significance | Policy Implication |
| :--- | :--- | :--- | :--- | :--- |
| **Lagged Eviction Rate ($l1\_percentage\_diff$)** | **+0.5408** | **0.000162** | **Extremely High** | **Time Inertia:** Eviction pressure is a persistent, cumulative process. The lagged rate is the strongest predictor of the current month's fluctuation. **Policy:** Prevention efforts must be continuous and timed for early intervention. |
| **Unemployment Rate ($unemployment\_rate$)** | **-0.0663** | **0.001694** | **High** | **Policy Buffer Effect:** The negative coefficient suggests that unemployment increases were significantly correlated with a *decrease* in relative eviction filings, indicating that government interventions (benefits, rent aid) successfully buffered the economic shock. |
| **CPI (cpi)** | +0.0006943 | 0.717740 | **None** | CPI does not have a statistically significant direct impact on eviction fluctuations in this model. |

#### 3. Key Technical Challenge (Collinearity)
* **Issue:** Urban Institute financial health indicators remain unchanged in the monthly time series, causing perfect collinearity with the model intercept.
* **Outcome:** These variables were automatically excluded from OLS estimation (`8 not defined because of singularities`).
* **Limitation:** This constrains the model's ability to assess the impact of **long-term** structural financial vulnerability.

### C. Model Validation and Performance

This project underwent rigorous multi-stage out-of-sample validation through time series segmentation (training set < 2023, test set $\geq$ 2023).

* **In-sample Error (Internal):** MAE was **$0.1225$**; RMSE was **$0.1711$**.
* **Out-of-sample Error (External):** MAE was **$0.1532$**; RMSE was **$0.1833$**.
* **Evaluation Conclusion:** The external errors are only slightly higher than the internal errors (MAE difference of $0.0307$), indicating that the model maintains good generalization capabilities when applied to new, unseen data from 2023 onwards.
* **Robustness:** Residual distribution plots confirm a strong similarity in the error distributions, supporting model robustness.