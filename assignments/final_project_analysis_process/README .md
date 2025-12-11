#  Eviction Predictor：Philadelphia Eviction Activity Prediction Model Based on Macroeconomic and Financial Health

This project integrates multi-source datasets（Eviction Lab、BLS、Urban Institute），An OLS linear regression model is employed to model and forecast fluctuations in Philadelphia's monthly eviction petition activity relative to its historical baseline (`percentage_diff`).

##  Project Setup, Data, and Objectives

### 1. Technical Environment
* **Language/Format：** R language, R Markdown (Rmd), Quarto。
* **Key R packages used by our group：** `tidyverse` (Data Processing/Manipulation), `sf` / `tigris` (Geospatial Analysis), `ggplot2` (Visualization), `broom` (Clean Model Output)。

### 2. Data Integration
The project integrates nine datasets provided by the instructor, enabling comprehensive analysis at both spatial (census tract) and temporal (monthly) scales.
* **Eviction data：** Census Area Application Volume/Rate (`tract_filings`)、Citywide Monthly Trends (`eviction_trends`)。
* **Macroeconomic data：** Monthly unemployment rate (`unemployment_monthly`)、Consumer Price Index（CPI）。
* **Financial Health Data：** Urban Institute Financial well-being indicators for residents (e.g., housing cost burden, income inequality)。

### 3. Selection of Dependent Variables (`percentage_diff`)
* **definition：** Monthly eviction application volume as a percentage deviation relative to the long-term historical baseline.
* **Advantages：** Compared to the original count, this variable is better suited to capture the marginal impact of **short-term economic shocks** on eviction pressures, making the model results more policy-relevant.

##  Detailed Analysis and Key Findings

### A. Geospatial and Temporal Trend Analysis 

#### 1. Geographic Concentration
* Analysis confirms significant geographic concentration of eviction activity in Philadelphia.
* **Hotspots:** Eviction petitions are highly concentrated in **North Philadelphia** and **West Philadelphia**, areas also exhibiting high rates of poverty, unemployment, and rental burden.
* **Implications:** This spatial pattern reflects the deep connection between housing instability and historical underinvestment in communities alongside structural inequalities.

2. Time Series Trends
* **Trend:** After experiencing a sharp suppression in deportation applications during 2020-2021 (due to COVID-19 stay orders), the volume accelerated its recovery starting in 2022 and stabilized in 2023-2024 (averaging approximately 1,060 applications per month).

B. OLS Regression Model Results and Policy Implications

The model employs ordinary least squares (OLS) fitting to assess the impact of macroeconomic indicators and lagged eviction activity on monthly eviction fluctuations.

1. Model Explanatory Power
* **Adjusted $R^2$:** Approximately **$0.865$**. The model exhibits exceptionally high explanatory power for monthly eviction fluctuations (`percentage_diff`), which is a critical factor.

#### 2. 核心预测因子分析

Eviction Predictor Project Key Summary
Project Objective: Utilize an OLS linear regression model to forecast fluctuations in Philadelphia's monthly eviction petition activity relative to historical baseline (percentage_diff).

Dependent Variable Selection: Employs percentage_diff (monthly petition volume's percentage deviation from historical baseline) to capture the marginal impact of macroeconomic disturbances on eviction pressure.

1. Data and Analysis Findings 1. Data Sources and Scope Integrated multi-source data from the Eviction Lab (eviction data), BLS (CPI/unemployment rate), and the Urban Institute (financial health). Time Series Span: January 2020 to December 2024 (72 months).

 2. Geographic and Temporal Trends Geographic Concentration: Eviction hotspots are highly concentrated in North and West Philadelphia, overlapping with areas of high poverty rates and high rental affordability burdens, reflecting structural inequalities. Temporal Trends: After a sharp suppression in eviction filings during 2020-2021, filings began recovering in 2022 and stabilized in 2023-2024 (averaging approximately 1,060 filings per month).

 3. Model Explanatory Power and Technical Challenges Model Explanatory Power (Adj. $R^2$): Approximately $0.865$, demonstrating exceptional explanatory power for monthly fluctuations. Technical Challenges: The Urban Institute's Financial Health Index exhibited collinearity with the model intercept due to insufficient variability in monthly data, necessitating its exclusion from OLS estimation. This limitation constrains the model's assessment of long-term structural risks.

#### 3. Key Technical Challenge (Collinearity)

* **Issue:** The Urban Institute's financial health indicators remain unchanged in monthly time series, resulting in perfect collinearity with the model intercept.
* **Outcome:** These variables are automatically excluded in OLS estimation.
* **Limitation:** This constrains the model's ability to assess the impact of **long-term** structural financial vulnerability on short-term eviction volatility.

C. Model Validation and Performance This project underwent rigorous multi-stage out-of-sample validation through time series segmentation (training set < 2023, test set ≥ 2023). In-sample error: The model achieved a Mean Absolute Error (MAE) of $0.1481$ and a Root Mean Square Error (RMSE) of $0.1982$. Out-of-sample error: The model achieved a Mean Absolute Error (MAE) of $0.2173$ and a Root Mean Square Error (RMSE) of $0.2464$. Evaluation Conclusion: The MAE and RMSE for the out-of-sample data are slightly higher than those for the in-sample data, but this is expected. This indicates that the model maintains good generalization capabilities when applied to new data from 2023 onwards, without exhibiting severe overfitting. Robustness: Observing the residual distribution plots reveals a certain similarity in the error distributions between the internal and external samples. This validation further supports the model's robustness.