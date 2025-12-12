# Air to Outcomes: An Exploratory Analysis of Pollution and Public Health

This repository contains the Quarto book for my EDAV final project, **“Air to Outcomes: An Exploratory Analysis of Pollution and Public Health.”**  
The project explores how **fine particulate matter (PM₂.₅)** relates to **asthma/COPD mortality** across the five New York City boroughs, and how these boroughs compare to the rest of New York State.

**Rendered project site:** `https://venkat10gitty.github.io/Air2Outcomes/`  

---

## Research questions

We focus on three main questions:

1. **Trends within NYC**  
   - Do annual PM₂.₅ levels differ across the five NYC boroughs (Bronx, Brooklyn, Manhattan, Queens, Staten Island), and how have they changed over time?

2. **Pollution–mortality relationship**  
   - Are higher borough–year PM₂.₅ levels associated with asthma/COPD mortality?

3. **NYC vs the rest of NY State**  
   - After normalizing by population, which boroughs are outliers, and how do they compare to other New York State counties?

All analysis is **exploratory** (no predictive modeling) and is presented with audience-ready graphics and commentary.

---

## Data sources

The project combines three main data streams:

- **EPA AQS (Air Quality System) – PM₂.₅**
  - Annual monitor summaries for parameter code **88101** (PM₂.₅ FRM/FEM).
  - Pulled for all New York State counties and then subset to NYC borough counties.
  - Accessed using the **RAQSAPI** R package.

- **CDC WONDER – Mortality data**
  - Underlying Cause of Death, ICD-10 codes **J40–J44 (COPD)** and **J45–J46 (asthma)**.
  - Grouped by **Year × County**, restricted to New York State.
  - Exported manually from the WONDER web interface as CSV (documented in the book).

- **U.S. Census / ACS – Population & socioeconomics**
  - ACS 5-year estimates for New York counties.
  - Variables:
    - `B01003_001` — total population  
    - `B19013_001` — median household income
  - Accessed via the **tidycensus** R package.

Spatial boundaries for NYC boroughs come from **tigris** county shapefiles.

The primary analysis window is **2010–2019**, where AQS, WONDER, and ACS coverage align cleanly.

---

## Repository structure

Key components of the repo:

- `index.qmd` – Landing page for the Quarto book.
- `intro.qmd` – Background and motivation.
- `data.qmd` – Data acquisition, cleaning, and construction of the borough and state-level panels.
- `results.qmd` – Main exploratory graphics and commentary.
- `conclusion.qmd` – Takeaways, limitations, and future directions.
- `_quarto.yml` – Quarto book configuration (title, author, theme, repo links).
- `docs/` – Rendered HTML output for GitHub Pages.
- `data_raw/`
  - `aqs/` – Raw EPA AirData annual concentration CSVs, plus derived RDS files.
  - `wonder/` – Exported CDC WONDER mortality CSV.
  - `acs/` – Raw ACS pulls via tidycensus, stored as RDS.

> **Note:** The raw data files themselves are *not* all committed to GitHub (size and licensing reasons). The book explains how to obtain and place them locally.

---

## Reproducibility & workflow

The project is designed so that another R user can reproduce the analysis end-to-end, **subject to a few external constraints** (API keys and WONDER exports).

### 1. Requirements

- **R** (recent version)  
- **Quarto** (via recent RStudio or standalone installation)
- R packages (installed automatically in the code, but you can pre-install):
  - `tidyverse`, `lubridate`, `janitor`, `glue`
  - `RAQSAPI`, `tidycensus`, `tigris`, `sf`
  - `ggridges`, `GGally`, `ggdist`, `biscale`, `gganimate`
  - and base graphics packages used by Quarto

### 2. API keys & credentials

You will need:

- **AQS credentials** for EPA AQS:
  - Sign up via `RAQSAPI::aqs_sign_up("youremail@example.com")`.
  - Store as environment variables `AQS_EMAIL` and `AQS_KEY`, or edit the code locally.

- **Census API key** for `tidycensus`:
  - Request at `https://api.census.gov/data/key_signup.html`.
  - Install via `tidycensus::census_api_key("YOUR_KEY", install = TRUE)` and restart R.

Both requirements, and how to set them, are documented in `data.qmd`.

### 3. WONDER mortality data (non-automated step)

CDC WONDER does not provide county-level breakdowns in an easy API call, so mortality data are:

1. Queried via the WONDER web interface:
   - Dataset: *Underlying Cause of Death, 1999–present*
   - Location: State = **New York**
   - Group by: **Year, County**
   - Codes: **J40–J44, J45–J46**
2. Exported as CSV and saved at:
   - `data_raw/wonder/wonder_ucd_asthma_copd_ny.csv`

The exact steps and filters are described inside `data.qmd`. This is the main non-automatic step in the workflow.
