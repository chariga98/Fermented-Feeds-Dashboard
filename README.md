#  Fermented Feeds Lab Analysis

> **R-based statistical analysis and interactive dashboard for evaluating the nutritional quality of farmer-made fermented livestock feeds** — benchmarked against commercial feeds, with regression modelling, PCA, clustering, and an AI-assisted Shiny interface.

---

##  Table of Contents

- [Overview](#overview)
- [Project Structure](#project-structure)
- [Features](#features)
- [Data Sources](#data-sources)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [Scripts](#scripts)
- [Dashboard](#dashboard)
- [Statistical Methods](#statistical-methods)
- [Outputs](#outputs)

---

## Overview

This project analyses laboratory results from farmer-produced fermented livestock feeds collected under the Agroecology Fund programme. It compares nutrient profiles (protein, energy, calcium, fibre, etc.) against commercial feed benchmarks, identifies what drives nutritional quality, and clusters farmers into fermentation practice typologies.

**The project answers three core questions:**
1. How does the nutritional quality of farmer-made fermented feeds compare to commercial feeds?
2. What ingredients, batch practices, and fermentation parameters drive better nutrient outcomes?
3. Can we identify distinct farmer typologies based on their fermentation strategies?

---

## Project Structure

```
Fermented_Lab_Analysis/
├── analysis.R                    # Main statistical analysis (regression, PCA, clustering)
├── regularisation.R              # LASSO & Ridge regression + PCA regression loop
├── visualisation.R               # Automated nutrient visualization pipeline (with bounds)
├── visualisation_basic.R         # Basic visualization pipeline (without bounds)
├── dashboard/
│   └── app.R                     # Shiny dashboard (interactive exploration + AI assistant)
│
├── Data/
│   ├── FarmentedLabResults.xlsx  # Main lab results dataset (one row per sample)
│   └── FeedsLab Dataset.xlsx     # Ingredient-level data (Go/Grow/Glow/Misc sheets)
│       ├── All                   # All samples combined
│       ├── Go                    # Energy ingredients (maize, bran, cassava, etc.)
│       ├── Grow                  # Protein ingredients (soya, calliandra, fish meal, etc.)
│       ├── Glow                  # Mineral/vitamin ingredients (kales, lime, eggshell, etc.)
│       └── Misc.                 # Additives & microbes
│
└── outputs/
    ├── lasso_results.csv
    ├── ridge_results.csv
    ├── pca_results.csv
    ├── model_performance.csv
    ├── Age_Effect_All_Nutrients.png
    └── Model_Table_Farmer_Experience.png
```

---

## Features

| Feature | Description |
|---|---|
|  Interactive Dashboard | Shiny app with filters for organization, farmer, ingredient, batch parameters & pH |
|  Benchmarking | Farmer feed bars plotted against commercial feed average (red dashed line) |
|  Nutrient Bounds | Reference ranges overlaid on all plots (gray = lower bound, red = upper bound) |
|  AI Assistant | Local Ollama/LLaMA3 integration for natural language queries on the data |
|  Regularised Regression | LASSO and Ridge models per nutrient, exported to CSV |
|  PCA Analysis | Nutrient profile PCA biplot + ingredient strategy PCA |
|  Farmer Clustering | k-means typologies by fermentation behaviour and ingredient strategy |
|  gt Tables | Color-coded (gray/green/red) nutrient tables per organization, sorted by value |
|  Password-Protected Download | Admin-gated data download within the dashboard |

---

## Data Sources

### `FarmentedLabResults.xlsx`
One row per feed sample. Key columns include:

| Column Group | Variables |
|---|---|
| **Identifiers** | `lab_id`, `farmer_name`, `organization`, `feed_type`, `microbe_source` |
| **Batch Parameters** | `previous_batches`, `batch_quantity_kg`, `age_of_batch_days`, `total_diversity`, `sample_p_h` |
| **Food Group Counts** | `number_go`, `number_grow`, `number_glow`, `number_misc` |
| **Nutrient Outcomes** | `crude_prot_dry`, `digestible_energy_dry`, `calcium_dry`, `phosphorus_dry`, `fibre_dry`, `total_ash_dry`, `crude_fat_dry`, `total_carb_dry` |
| **Ingredient Flags** | Binary (0/1) columns from `maize` to `cowpea_leaves` |

### `FeedsLab Dataset.xlsx`
Ingredient-level data split into four food group sheets (Go, Grow, Glow, Misc.), each with binary ingredient columns and the same 9 nutrient outcome columns.

---

## Nutrient Reference Ranges

The following reference bounds are used across all plots and tables:

| Nutrient | Lower Bound | Upper Bound |
|---|---|---|
| Crude Protein (%) | 15 | 24 |
| Digestible Energy (kcal/kg) | 2700 | 3200 |
| Calcium (%) | 0.65 | 4.5 |
| Phosphorus (%) | 0.15 | 0.22 |
| Total Ash (%) | 4 | 14 |
| Fibre (%) | 2 | 10 |
| Crude Fat (%) | 1 | 10 |

---

## Prerequisites

- **R** ≥ 4.1.0
- **Ollama** (optional, for AI assistant) — install from [ollama.ai](https://ollama.ai) and run `ollama run llama3`

---

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/your-username/fermented-feeds-analysis.git
cd fermented-feeds-analysis
```

### 2. Install R packages

```r
install.packages(c(
  # Core analysis
  "tidyverse", "readxl", "janitor", "broom", "broom.mixed",
  "lme4", "glmnet", "FactoMineR", "factoextra", "modelsummary",
  "GGally", "patchwork", "viridis", "knitr",

  # Dashboard
  "shiny", "bslib", "shinyWidgets", "plotly", "DT", "scales",
  "glue", "forcats",

  # Tables
  "gt", "gtExtras",

  # AI assistant (optional)
  "rollama"
))
```

### 3. Configure data paths

Update the file paths at the top of each script:

```r
# analysis.R / regularisation.R
path <- "Data/FarmentedLabResults.xlsx"

# visualisation.R / dashboard/app.R
path_1 <- "Data/FeedsLab Dataset.xlsx"

# dashboard/app.R
Farmer_raw <- read_excel("Data/Farmer_data.xlsx")
Feeds_raw  <- read_excel("Data/Feeds_data.xlsx")
```

---

## Usage

### Run the Shiny Dashboard

```r
shiny::runApp("dashboard/app.R")
```

### Run the Statistical Analysis

```r
source("analysis.R")        # Mixed models, PCA, clustering, age-effect plots
source("regularisation.R")  # LASSO, Ridge, PCA regression — exports CSVs
```

### Run the Visualization Pipeline

```r
source("visualisation.R")   # Full pipeline with nutrient reference bounds
```

---

## Scripts

### `analysis.R` — Core Statistical Analysis

- **Exploratory plots**: histograms of all nutrient outcomes; correlation matrix (`GGally::ggpairs`)
- **OLS regression**: effect of farmer experience (`previous_batches`), ingredient diversity, and batch age on each nutrient
- **Mixed-effects models** (`lmer`): effect of food group counts, batch quantity, and microbe source on nutrients, with organization as a random effect
- **Age effect plots**: LOESS-smoothed nutrient trajectories over batch age, colored by ingredient diversity tier (< 10, 10–18, ≥ 19)
- **Nutrient PCA**: biplot of nutrient profiles across fermented feed samples
- **Ingredient strategy clustering**: k-means (k=3) on ingredient PCA components
- **Farmer typology clustering**: k-means (k=3) on fermentation behaviour variables

### `regularisation.R` — Penalised Regression Loop

Runs automatically across all 6 nutrients:

1. **LASSO** (`alpha = 1`): feature selection via cross-validated lambda
2. **Ridge** (`alpha = 0`): coefficient shrinkage, retains all predictors
3. **PCR** (PCA Regression): regresses nutrients on first 2 principal components
4. **Model performance table**: R², Adjusted R², RMSE per nutrient

### `visualisation.R` — Automated Plot Pipeline

For each nutrient, generates 6 plots per iteration:
- Distribution histogram (with reference bound lines)
- Farmer-level scatter (Lab ID × nutrient, colored by organization)
- Material scatter for Go, Grow, Glow, and Misc ingredients

### `dashboard/app.R` — Interactive Shiny App

See [Dashboard](#dashboard) section below.

---

## Dashboard

### Sidebar Filters
- **Metric selector**: choose which nutrient to analyze
- **Organization / Farmer / Ingredient** multi-select filters
- **Batch parameter sliders**: previous batches, quantity (kg), age (days), diversity, pH
- **Password-protected download** of filtered data

### Tabs

| Tab | Content |
|---|---|
| **Statistical Overview** | KPI boxes (total samples, commercial count, fermented count, avg pH), summary stats tables by feed type and sample type (mean, median, SD, CV%), AI assistant |
| **Variation Shapes** | Density plots split by analysis group (Abaniibi A/B, Witharaga, Individual) and combined by feed type |
| **Farmer Results** | Benchmarking bar chart vs. commercial average (red line) + most common ingredients bar |
| **Metrics** | Full DT table of all 13 metrics with stats; average nutrient levels bar chart |

### AI Assistant
The dashboard includes a local AI assistant powered by **Ollama (LLaMA3)**. It receives a data context summary (sample count, current metric, average value, top ingredient) and answers natural language questions. Requires Ollama running locally:

```bash
ollama run llama3
```

---

## Statistical Methods

| Method | Purpose | Script |
|---|---|---|
| OLS Linear Regression | Effect of experience & batch parameters on nutrients | `analysis.R` |
| Linear Mixed Models (`lmer`) | Organization as random effect, ingredient counts as fixed | `analysis.R` |
| LASSO Regression | Feature selection across nutrients | `regularisation.R` |
| Ridge Regression | Coefficient shrinkage across nutrients | `regularisation.R` |
| PCA Regression (PCR) | Dimensionality reduction before regression | `regularisation.R` |
| PCA Biplot | Nutrient profile structure | `analysis.R` |
| k-means Clustering | Ingredient strategy + farmer typologies | `analysis.R` |

---

## Outputs

| File | Description |
|---|---|
| `lasso_results.csv` | LASSO coefficients per nutrient |
| `ridge_results.csv` | Ridge coefficients per nutrient |
| `pca_results.csv` | PCR coefficients (PC1, PC2) per nutrient |
| `model_performance.csv` | R², Adj R², RMSE per nutrient |
| `Age_Effect_All_Nutrients.png` | 2×3 panel of age-effect LOESS plots |
| `Model_Table_Farmer_Experience.png` | OLS regression table (modelsummary) |

---

*Source: Fermented Feeds Laboratory Evaluation Data (Agroecology Fund Project)*
