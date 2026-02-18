# Fermented Feeds Lab Analysis & Dashboard

This repository contains a **complete analytical pipeline and interactive dashboard** for exploring **laboratory nutrient results of fermented poultry feeds**. It integrates:

- Data cleaning & harmonization  
- Exploratory data analysis (EDA)  
- Automated visualization pipelines  
- Statistical modeling & inference  
- An interactive **Shiny dashboard with AI-assisted interpretation**

The project is designed to support **farmer research networks, agroecology programs, and feed optimization research** by making lab data **transparent, interpretable, and actionable**.

---

## Project Structure

.
├── app.R # Shiny interactive dashboard
├── Farmer_data.xlsx # Farmer-level lab results
├── Feeds_data.xlsx # Feed ingredient composition data
├── FeedsLab Dataset.xlsx # Full analytical dataset (GO / GROW / GLOW / MISC)
├── FermentedLabResults.xlsx # Master modeling dataset
├── scripts/
│ ├── nutrient_visuals.R # Automated visualization pipeline
│ ├── nutrient_tables.R # Automated nutrient summary tables
│ ├── modeling_pipeline.R # PCA, LASSO, Ridge, regression modeling
│ └── data_cleaning.R # Data cleaning & harmonization
└── README.md


---

## Core Components

### 1. Interactive Shiny Dashboard (`app.R`)

A **fully interactive web dashboard** for:

- Exploring nutrient distributions  
- Comparing fermented vs commercial feeds  
- Benchmarking farmer batches  
- Visualizing ingredient patterns  
- Statistical summaries by group  
- AI-assisted interpretation of live data  

#### Dashboard Pages

| Tab | Purpose |
|------|-----------|
| Statistical Overview | Summary statistics & KPIs |
| Variation Shapes | Distribution & density plots |
| Farmer Results | Farmer benchmarking & ingredient patterns |
| Metrics | Nutrient summary tables & averages |
| AI Assistant | Natural language insights |

#### Key Features

- Multi-level filtering (organization, farmer, ingredients, batch parameters)  
- KPI summary cards  
- Interactive Plotly charts  
- Password-protected data download  
- Local AI assistant powered by **Ollama + LLaMA3**

---

### 2. Nutrient Visualization Pipeline

Automatically generates **distribution plots, farmer scatter plots, and ingredient scatter plots** for each nutrient across:

- GO  
- GROW  
- GLOW  
- MISC  

#### Nutrients Analysed

total_ash_dry
fibre_dry
crude_fat_dry
crude_prot_dry
non_n_free_extract_dry
total_carb_dry
digestible_energy_dry
calcium_dry
phosphorus_dry


Each nutrient produces:

- Histogram (distribution)
- Farmer-level scatter
- Ingredient-level scatter

This enables **systematic comparison of variability and formulation strategies**.

---

### 3. Automated Nutrient Tables

For each nutrient, the pipeline generates **presentation-ready tables** that:

- Rank farmers within organizations  
- Use consistent formatting  
- Automatically balance layout across organizations  

Tables display:

Farmer name — nutrient value (3 decimal places)


---

### 4. Statistical Modeling Pipeline

This section investigates **which fermentation and batch parameters predict nutrient outcomes**.

#### Predictors

previous_batches
total_diversity
age_of_batch_days


#### Outcomes

calcium_dry
phosphorus_dry
total_ash_dry
crude_prot_dry
digestible_energy_dry
fibre_dry


#### Methods Applied

| Method | Purpose |
|----------|----------|
| LASSO regression | Feature selection |
| Ridge regression | Stable coefficient estimation |
| PCA + Regression | Dimensionality reduction |
| RMSE + R² | Model evaluation |

#### Outputs

lasso_results.csv
ridge_results.csv
pca_results.csv
model_performance.csv


---

### 5. Data Cleaning & Harmonization

All datasets undergo:

- Standardized variable naming  
- Explicit data type conversion  
- Numeric sanitation (ND / NT / text → NA)  
- Ingredient matrix validation  
- Cross-sheet harmonization  

This ensures **consistent modeling and visualization behavior** across scripts.

---

## Installation & Setup

### 1. Install Required R Packages

```r
install.packages(c(
  "shiny","dplyr","ggplot2","tidyr","bslib","scales","plotly",
  "readxl","shinyWidgets","janitor","forcats","DT","gt",
  "glmnet","FactoMineR","factoextra","modelsummary","broom",
  "GGally","patchwork","purrr","stringr","viridis","rollama"
))
2. (Optional) Enable Local AI Assistant
Install Ollama and pull the LLaMA3 model:

ollama pull llama3
Run:

ollama run llama3
The dashboard automatically connects using the rollama R package.

3. Run Dashboard
shiny::runApp("app.R")
Data Requirements
Farmer_data.xlsx
Must contain:

Category	Example Columns
Identifiers	lab_id, farmer_name, organization
Batch	previous_batches, batch_quantity_kg
Fermentation	age_of_batch_days, sample_p_h
Nutrients	protein, energy, minerals, fibre
Feed Type	fermented, commercial
Feeds_data.xlsx
Column	Description
lab_id	Sample identifier
feed_ingredient	Ingredient used
FeedsLab Dataset.xlsx
Must include sheets:

All
Go
Grow
Glow
Misc.
Each representing feed ingredient categories.

Analytical Questions Addressed
How does fermented feed quality compare to commercial feed?

Which ingredients produce the best nutrient profiles?

How consistent are farmers’ fermentation processes?

Which batch parameters predict feed quality?

How does ingredient diversity influence protein and energy levels?

Intended Users
Agroecology researchers

Farmer research networks

Feed formulation specialists

Extension & advisory teams

Agricultural impact programs

**#Author & Project Context**
Developed for applied research on fermented poultry feeds, supporting:

Farmer-led experimentation

Participatory research networks

Agroecological transitions

Evidence-based feed formulation

