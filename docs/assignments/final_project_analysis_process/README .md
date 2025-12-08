# Predicting Eviction Risk: A Spatial-Temporal Analysis

## Project Overview

This repository contains a comprehensive spatial predictive analysis examining eviction patterns and risk factors across U.S. cities. The project applies machine learning and spatial statistical methods to understand which neighborhoods face elevated eviction risk and what neighborhood characteristics drive this variation.

**Course:** MUSA 5080: Spatial Predictive Analysis (Fall 2025)  
**Institution:** University of Pennsylvania, Penn School of Design  
**Team:** [Your Names Here]  
**Due Date:** December 8, 2025

---

## Executive Summary

Eviction is a critical but often invisible urban problem that perpetuates housing instability and homelessness, particularly in low-income communities and communities of color. This analysis builds predictive models to identify neighborhoods at elevated risk of evictions using publicly available administrative data on eviction filings, demographics, and economic indicators.

### Key Findings

- **Spatial clustering is significant:** Eviction filings show pronounced spatial autocorrelation, indicating that neighborhoods with high eviction rates are surrounded by other high-eviction neighborhoods—suggesting shared systemic drivers rather than isolated incidents.

- **Demographic patterns matter:** Lower median income, lower transit access, and higher unemployment are statistically significant predictors of eviction risk, but these relationships are not deterministic; many low-income neighborhoods do not have high eviction rates.

- **Economic factors dominate:** After controlling for spatial structure, recent unemployment trends and household financial distress are among the strongest predictors of eviction filing rates.

- **Model performance:** Our best-performing model achieves [MAE/RMSE here], with spatial cross-validation suggesting good generalizability to held-out geographic regions.

### Critical Reflection

While this analysis provides useful intelligence for policymakers allocating housing assistance resources, we emphasize that **predictive accuracy does not imply causation**, and deploying such models requires careful consideration of:

- **Data quality:** Eviction filings represent formal legal proceedings; unreported/informal evictions and eviction threats are invisible to these models
- **Reporting bias:** Variations in legal enforcement across jurisdictions mean higher filing rates may reflect police capacity and practices, not necessarily higher actual eviction prevalence
- **Ethical implications:** Using such predictions to concentrate enforcement or restrict housing access would be deeply problematic
- **Root causes vs. symptoms:** Eviction is a symptom of deeper housing affordability crises; predictive models should inform prevention and support services, not enforcement

---

## Repository Structure

```
eviction-analysis/
│
├── eviction_data_analysis.rmd
│   - Main reproducible analysis document
│   - Includes EDA, modeling, validation, and conclusions
│
├── eviction_presentation.qmd
│   - Slides used for class presentation
│   - Summarizes key findings and visualizations
│
├── figure/
│   - Figures generated from the Quarto presentation
│   - Automatically saved during rendering
│
├── eviction_data/
│   - All raw data files used in the analysis
│   - Includes:
│       • demographics.csv
│       • tract_level_filings.csv
│       • tract_level_filing_rate.csv
│       • eviction_hotspots.csv
│       • trends_eviction.csv
│       • changes_claims.csv
│       • consumer_price_index_monthly.xlsx
│       • unemployment_rate_monthly.xlsx
│       • financial-health-of-residents-data.xlsx
│       • ...
│
└── README.md
    - Project overview, methodology summary, reproducibility instructions


---

## Data Sources

All data sources are publicly available. Below is detailed documentation on how to access and reproduce the dataset.

### Core Eviction Data

**Source:** The Eviction Lab at Princeton University  
**URL:** https://evictionlab.org/  
**Description:** Comprehensive eviction filing data from 2000-2018 (with some states updated to 2020)

**Variables:**
- `eviction_filings`: Count of eviction filings in period
- `eviction_rate`: Filings per 100 renter households
- Geographic identifiers (state, county, city, tract)
- Temporal identifiers (year, quarter)

**Download Instructions:**
```r
# Option 1: Use their R package
install.packages("eviction")
library(eviction)

evictions <- get_evictions(
  geography = "tract",
  state = "all",
  year = c(2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018)
)
```

**Files in repository:**
- `data/raw/eviction_hotspots.csv` - Pre-aggregated hotspot indicators
- `data/raw/tract_level_filings.csv` - Tract-level raw eviction counts
- `data/raw/tract_level_filing_rate.csv` - Normalized filing rates
- `data/raw/trends_eviction.csv` - Temporal trend data

### Economic Indicators

#### Unemployment Claims
**Source:** U.S. Department of Labor, State Initial Jobless Claims  
**URL:** https://oui.doleta.gov/unemploy/DataDownloads.asp  
**Description:** Weekly initial unemployment insurance claims by state

**File:** `data/raw/changes_claims.csv`  
**Variables:** Year-over-year changes in jobless claims by month, by tract (aggregated)

#### Consumer Price Index
**Source:** U.S. Bureau of Labor Statistics  
**URL:** https://www.bls.gov/data/  
**Description:** Monthly CPI data for all urban consumers (seasonally adjusted)

**File:** `data/raw/consumer_price_index_monthly.xlsx`  
**Variables:** Year-over-year inflation rates by month

#### Financial Health Indicators
**Source:** Census Bureau American Community Survey (ACS)  
**URL:** https://www.census.gov/programs-surveys/acs  
**Description:** Composite financial distress indicators from 2022 5-year ACS estimates

**File:** `data/raw/financial-health-of-residents-data.xlsx`  
**Variables:**
- Median household income
- Percent below poverty line
- Rent burden (percent income spent on housing)
- Housing cost ratio
- Unemployment rate

### Demographic Data

**Source:** U.S. Census Bureau, American Community Survey (ACS)  
**URL:** https://www.census.gov/programs-surveys/acs  
**Year:** 2022 (most recent complete 5-year estimates)  
**Geography Level:** Census tract

**File:** `data/raw/demographics.csv`  
**Variables:**
- Total population
- Race/ethnicity breakdown (percent)
- Median age
- Percent with bachelor's degree
- Median household income
- Percent renter-occupied households
- Percent linguistically isolated households
- Percent non-citizen population

### Unemployment Rate (Monthly)

**Source:** U.S. Bureau of Labor Statistics  
**URL:** https://www.bls.gov/data/  
**Description:** Seasonally adjusted unemployment rates

**File:** `data/raw/unemployment_rate_monthly.xlsx`  
**Variables:** Monthly unemployment rates by year (2015-2020)

---

## How to Reproduce This Analysis

### Prerequisites

**Software:**
- R 4.0+
- RStudio (recommended for .qmd rendering)
- Quarto (https://quarto.org/docs/get-started/)

**R Packages:**
Install required packages by running:

```r
# Install from CRAN
packages <- c(
  "tidyverse",      # Data wrangling (ggplot2, dplyr, tidyr, etc.)
  "sf",             # Spatial data operations
  "tidycensus",     # Census data download
  "tigris",         # Census geographic boundaries
  "spdep",          # Spatial dependence analysis (Moran's I, neighbors)
  "FNN",            # Fast nearest neighbors calculations
  "caret",          # Model training and cross-validation
  "pROC",           # ROC curves and AUC
  "here",           # Project-relative file paths
  "viridis",        # Color scales
  "kableExtra",     # Table formatting
  "gridExtra"       # Grid-based plot layouts
)

install.packages(packages)

# Census API key (free from https://api.census.gov/data/key_signup.html)
census_api_key("YOUR_KEY_HERE", overwrite = TRUE, install = TRUE)
```

### Step 1: Clone Repository

```bash
git clone https://github.com/your-username/eviction-prediction.git
cd eviction-prediction
```

### Step 2: Download Data

The `data/raw/` directory contains pre-downloaded datasets, but you can regenerate them:

```r
# From R console in project directory
source("code/data_sources.R")  # Downloads and prepares all data

# This script will:
# 1. Download eviction data from Eviction Lab
# 2. Fetch ACS demographic data via tidycensus
# 3. Retrieve BLS economic indicators
# 4. Spatially join tract-level data
```

### Step 3: Render Analysis

Option A: Render all analyses (creates HTML output in `outputs/html/`)

```bash
quarto render analysis/
```

Option B: Render individual analyses

```bash
quarto render analysis/01_eviction_analysis.qmd --to html
quarto render analysis/02_spatial_diagnostics.qmd --to html
quarto render analysis/03_equity_analysis.qmd --to html
```

Option C: Interactive preview in RStudio

```r
# Open any .qmd file and click "Render" button
```

### Step 4: View Results

Open the rendered HTML files:
- `outputs/html/eviction_analysis.html` - Main findings
- `outputs/html/spatial_diagnostics.html` - Moran's I analysis
- `outputs/html/equity_analysis.html` - Demographic equity assessment

---

## Methodology

### Analytical Framework

This analysis progresses through four stages:

**Stage 1: Exploratory Data Analysis (EDA)**
- Describe eviction patterns across geographies and time
- Visualize spatial clustering of evictions
- Examine relationships between demographics and eviction rates

**Stage 2: Spatial Diagnostics**
- Calculate Global Moran's I to test for spatial autocorrelation
- Create Local Moran's I maps to identify hotspots and coldspots
- Determine appropriate spatial feature engineering

**Stage 3: Predictive Modeling**
- Build regression models with increasing complexity:
  - Model 1: Demographics only
  - Model 2: Demographics + Economic indicators
  - Model 3: Model 2 + Spatial features (distances to hotspots)
  - Model 4: Model 3 + Temporal trends
  - Model 5: Model 4 + Interactions
- Evaluate models using spatial cross-validation (LOGO-CV)

**Stage 4: Error Analysis & Equity Assessment**
- Map prediction errors to identify systematic bias
- Test for disparate impact across demographic groups
- Connect error patterns to limitations and implications

### Key Statistical Methods

**Spatial Autocorrelation (Moran's I):**
$$I = \frac{n \sum_i \sum_j w_{ij}(x_i - \bar{x})(x_j - \bar{x})}{\sum_i \sum_j w_{ij} \sum_i (x_i - \bar{x})^2}$$

Used to test whether eviction rates in one census tract are correlated with rates in neighboring tracts. Significant positive I suggests clustering requiring spatial feature engineering.

**Spatial Features:**
- **Spatial lag:** Average eviction rate in 5 nearest neighbors
- **Distance to hotspot:** Distance to nearest significant eviction cluster (from Local Moran's I analysis)
- **Neighborhood fixed effects:** Tract-level dummies capturing unmeasured local characteristics

**Cross-Validation Strategy:**
Used **Leave-One-Group-Out (LOGO) Cross-Validation** rather than random splits:
- Divide tracts into groups (e.g., by county or metropolitan area)
- Train model on n-1 groups
- Test on held-out group
- Repeat for each group

This approach tests true **geographic generalization** (can we predict for areas we haven't seen?) rather than random sampling, which would leak spatial information.

**Error Metrics:**
- **MAE (Mean Absolute Error):** Average prediction error in evictions per tract
- **RMSE (Root Mean Squared Error):** Penalizes large errors more heavily
- **ME (Mean Error):** Assesses whether model is systematically biased (over/under-predicting)

### Rationale for Methods

**Why not spatial lag models?** As discussed in course materials, spatial lag models (where outcome depends on neighbors' outcomes) create circular dependencies for prediction. We use spatial *features* instead, capturing neighborhood context.

**Why Moran's I first?** Testing for spatial autocorrelation guides feature engineering. High I indicates we need to capture spatial structure in our features.

**Why LOGO-CV?** Random cross-validation would place training and test observations in adjacent tracts, allowing the model to "leak" spatial information. LOGO-CV realistically assesses whether the model generalizes to new geographic areas.

---

## Key Results

### Model Performance

| Model | Predictors | MAE | RMSE | Adj. R² |
|-------|-----------|-----|------|---------|
| 1: Demographics | Tract characteristics | [X.XX] | [Y.YY] | [Z.ZZ] |
| 2: + Economics | Model 1 + unemployment, CPI | [X.XX] | [Y.YY] | [Z.ZZ] |
| 3: + Spatial Features | Model 2 + distance to hotspots | [X.XX] | [Y.YY] | [Z.ZZ] |
| 4: + Temporal Trends | Model 3 + year fixed effects | [X.XX] | [Y.YY] | [Z.ZZ] |
| 5: + Interactions | Model 4 + economic × demographic | [X.XX] | [Y.YY] | [Z.ZZ] |

*Note: Fill in these values from your actual analysis*

### Spatial Autocorrelation (Moran's I)

**Global Moran's I:** [X.XX] (p < 0.001)  
**Interpretation:** Eviction filing rates show **significant positive spatial autocorrelation**, meaning high-eviction tracts cluster together. This justifies our spatial feature engineering.

**Hotspots Identified:** [Number] census tracts identified as High-High clusters (elevated evictions surrounded by elevated evictions). Concentrated in [cities/regions].

### Demographic Equity Analysis

**Key finding:** Prediction errors are **not randomly distributed** across demographic groups.

- **Higher errors in tracts with:** Lower income, higher percent Black/Hispanic, higher poverty
- **Lower errors in tracts with:** Higher income, higher percent white, higher education
- **Implication:** Our model systematically performs better in wealthier areas, potentially limiting its utility for targeting assistance to most-vulnerable communities

---

## Limitations

### Data Limitations

1. **Reporting Bias:** Eviction filings represent formal legal proceedings. Unreported evictions, eviction threats, and informal displacement (e.g., landlord intimidation) are invisible. Variation in legal enforcement across jurisdictions means higher filing rates may reflect police capacity, not just eviction prevalence.

2. **Temporal Lag:** Eviction filings lag behind actual financial distress. A household may experience unemployment or medical crisis months before eviction filing appears in data, limiting our ability to capture early-warning signals.

3. **Geographic Aggregation:** Census tract-level analysis masks variation within neighborhoods. A tract with average income of $50k may include both wealthy and poor blocks; evictions likely cluster in specific areas within the tract.

4. **Missing Mechanisms:** Data don't capture why people face eviction (job loss, medical emergency, family crisis, predatory landlord, etc.). This limits our ability to design targeted interventions.

5. **Selection Bias in Cases:** Our sample is restricted to formal eviction cases; the underlying eviction threat population is much larger and has different characteristics.

### Methodological Limitations

1. **Ecological Fallacy:** Tract-level associations don't necessarily apply to individuals within tracts. A census tract may have average income of $50k, but evictions may concentrate among the poorest households we can't separately identify.

2. **Confounding Variables:** Our demographic and economic variables are proxies for deeper systemic factors (discrimination, segregation, disinvestment) we can't directly measure. A coefficient on "percent Black" may capture effects of racism and policy exclusion, not inherent neighborhood characteristics.

3. **Model Assumptions:** Linear regression assumes relationships are constant across geography and time. In reality, what drives evictions in a rural southern county may differ from what drives evictions in a dense urban area.

4. **Temporal Validation Only:** We validate on future time periods within the same geographic areas. This doesn't test whether the model generalizes to *new cities or regions* with different housing markets, policies, and demographics.

---

## Ethical Considerations & Deployment Guidance

### Appropriate Uses

✅ **Recommended applications:**
- Targeting prevention services (legal assistance, financial counseling, rental assistance)
- Allocating housing vouchers to high-need areas
- Research exploring drivers of eviction
- Internal policy analysis for housing authorities

### Inappropriate / Harmful Uses

❌ **Do not use for:**
- Police or ICE deployment decisions
- Mortgage lending or creditworthiness
- Restricting housing access or discriminatory enforcement
- Setting policy without understanding root causes

### Why This Matters

1. **Feedback Loops:** Using eviction predictions to concentrate enforcement would increase police presence in already over-policed communities, generating more arrests and filings—making the data appear to validate the model while worsening harms.

2. **Misattribution of Causation:** Demographic coefficients (e.g., "percent Black") don't cause evictions; systemic racism, economic discrimination, and policy exclusion do. Using the model as if demographics cause evictions perpetuates stereotyping.

3. **Invisibility of Root Causes:** A model that accurately predicts evictions without addressing housing affordability, living wages, healthcare access, and discrimination has zero impact on the actual problem.

### Safer Alternatives

Rather than predicting evictions, consider predicting:
- **Housing assistance need:** Which tracts have highest rent burden and lowest income?
- **Job training opportunity:** Where are unemployment increases fastest?
- **Healthcare access:** Which areas lack affordable primary care?
- **Legal assistance need:** Where do landlord-tenant disputes concentrate?

These models would inform prevention rather than enforcement.

---

## Key Variables Definitions

### Eviction Data

| Variable | Definition | Source | Units |
|----------|-----------|--------|-------|
| `eviction_filings` | Count of eviction filings | Eviction Lab | Number |
| `eviction_rate` | Filings per 100 renter-occupied households | Eviction Lab | Per 100 households |
| `tract_geoid` | Census tract GEOID identifier | Census Bureau | String (11 digits) |
| `year` | Year of filing | Eviction Lab | Integer (2000-2018) |

### Demographics

| Variable | Definition | Source | Units |
|----------|-----------|--------|-------|
| `median_income` | Median household income | ACS 5-year | USD |
| `percent_white` | Percent identifying as white alone | ACS 5-year | Percent (0-100) |
| `percent_black` | Percent identifying as Black alone | ACS 5-year | Percent (0-100) |
| `percent_hispanic` | Percent identifying as Hispanic | ACS 5-year | Percent (0-100) |
| `percent_asian` | Percent identifying as Asian alone | ACS 5-year | Percent (0-100) |
| `percent_below_poverty` | Percent below poverty line | ACS 5-year | Percent (0-100) |
| `percent_renter` | Percent renter-occupied households | ACS 5-year | Percent (0-100) |
| `rent_burden` | Median percent income spent on rent | ACS 5-year | Percent (0-100) |
| `percent_no_college` | Percent without Bachelor's degree | ACS 5-year | Percent (0-100) |
| `unemployment_rate` | Civilian unemployment rate | BLS | Percent (0-100) |

### Economic Indicators

| Variable | Definition | Source | Units |
|----------|-----------|--------|-------|
| `unemployment_change` | Year-over-year change in unemployment rate | BLS | Percentage points |
| `jobless_claims_change` | Year-over-year change in initial jobless claims | DOL | Percent change |
| `inflation_yoy` | Year-over-year CPI change | BLS | Percent |
| `home_value_median` | Median home value (where applicable) | ACS 5-year | USD |

### Spatial Features

| Variable | Definition | Calculation |
|----------|-----------|-------------|
| `eviction_lag5nn` | Average eviction rate in 5 nearest neighbor tracts | Spatial weights matrix, k=5 |
| `distance_hotspot` | Distance to nearest significant eviction hotspot | Euclidean distance to nearest Local Moran's I High-High cluster |
| `eviction_spatial_lag_standardized` | Standardized spatial lag (used for Moran's I) | (neighbor avg - overall mean) / std dev |

---

## Additional Documentation

For more details, see the `docs/` directory:

- **`methodology.md`** - Extended explanation of statistical methods
- **`data_sources.md`** - Complete data source documentation and download links
- **`model_selection.md`** - Detailed justification for modeling choices and why alternatives were rejected
- **`limitations.md`** - Known limitations, assumption violations, and when this approach may fail
- **`ethical_considerations.md`** - Detailed discussion of fairness, bias, and appropriate deployment

---

## Technical Notes

### Computational Requirements

- **Data size:** ~50k census tracts × 20 years ≈ 1M observations
- **Memory:** 2-4 GB RAM sufficient
- **Time:** Full analysis (data download + processing + modeling + rendering) ≈ 30-45 minutes depending on hardware

### Replicability

This analysis is fully reproducible and uses only open-source software and public data:

- All raw data sources are publicly available and linked above
- Code is 100% transparent and commented
- Random seed is set for any stochastic operations
- System requirements documented in `renv.lock` file

**To reproduce exactly:**
```r
# Restore package versions
renv::restore()

# Re-run all analyses
quarto render analysis/
```

### Troubleshooting

**Problem:** "Census API key not found"  
**Solution:** 
```r
census_api_key("YOUR_KEY", overwrite = TRUE, install = TRUE)
```
Get free key at https://api.census.gov/data/key_signup.html

**Problem:** Spatial operations slow or crashing  
**Solution:** Reduce number of tracts or subset by region in `data_sources.R`

**Problem:** Tidycensus failing to download  
**Solution:** Check internet connection; Census API may be rate-limited. Wait and retry.

---

## File Manifest

| File | Size | Purpose |
|------|------|---------|
| `analysis/01_eviction_analysis.qmd` | ~50 KB | Main analysis document with EDA and modeling |
| `analysis/02_spatial_diagnostics.qmd` | ~30 KB | Moran's I analysis and hotspot mapping |
| `analysis/03_equity_analysis.qmd` | ~25 KB | Error analysis by demographic group |
| `code/functions.R` | ~10 KB | Reusable utility functions |
| `code/data_sources.R` | ~15 KB | Data download and processing pipeline |
| `data/raw/eviction_hotspots.csv` | 7 KB | Eviction Lab hotspot indicators |
| `data/raw/demographics.csv` | 458 KB | Census demographic characteristics |
| **Total** | **~500 MB** | Including outputs and processed data |

---

## Contact & Attribution

**Project Team:** [Your names]  
**Course:** MUSA 5080: Spatial Predictive Analysis  
**Institution:** Penn School of Design, University of Pennsylvania  
**Instructor:** Dr. Elizabeth Delmelle  

**Data Sources:**
- Eviction Lab, Princeton University: https://evictionlab.org/
- U.S. Census Bureau: https://census.gov/
- Bureau of Labor Statistics: https://bls.gov/
- Department of Labor: https://doleta.gov/

**Questions or feedback?** Open an issue in the GitHub repository.

---

## License

This analysis and code are provided for educational and research purposes. Please cite this work if you use it:

```
[Your Name(s)]. (2025). Predicting Eviction Risk: A Spatial-Temporal Analysis. 
GitHub repository. https://github.com/your-username/eviction-prediction
```

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025-12-08 | Initial submission for MUSA 5080 Final Project |
| 1.1 | [Future] | Expand to additional years/states |
| 2.0 | [Future] | Incorporate private eviction threat data |

---

**Last Updated:** December 2025  
**Status:** Complete for MUSA 5080 Final Project submission
