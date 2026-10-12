# London Café Geography and Business Dynamics, 2020–2025

**Spatial Distribution, Associated Factors and Business Dynamics of the Café Industry in London (2020–2025): Integrating Spatial Analysis and Machine Learning**

## Project Overview

This repository presents the research conducted for my MSc Urban Spatial Science dissertation at University College London.

The study investigates the spatial distribution of cafés in Greater London, the neighbourhood characteristics associated with café density, and the predictability of café business dynamics between 2020 and 2025.

Historical OpenStreetMap points of interest were integrated with demographic, socioeconomic, housing and transport accessibility data. The analysis combines spatial autocorrelation analysis, Ordinary Least Squares regression, spatial dependence diagnostics, machine-learning models and POI-based Doc2Vec embeddings.

The project distinguishes between explanatory and predictive analysis:

- OLS models examine conditional associations between neighbourhood characteristics and café density.
- Logistic Regression, Random Forest and XGBoost compare the predictability of café survival, new openings and MSOA-level growth.
- Doc2Vec experiments test whether POI composition provides additional predictive information beyond conventional structured variables.

The results represent statistical associations and predictive relationships rather than causal effects.

> **Repository status:** This README is based on the submitted dissertation. The original notebooks, reusable scripts, data documentation and reproducibility workflow are currently being reorganised. Until this process is complete, the repository should not be considered fully reproducible.

## Dissertation Information

- **Author:** Yanxi Ren
- **Programme:** MSc Urban Spatial Science
- **Institution:** University College London
- **Module:** CASA0010 Dissertation
- **Supervisor:** Dr Maarten Vanhoof
- **Submission date:** 20 August 2026
- **Study area:** Greater London
- **Primary spatial unit:** 2021 Middle Layer Super Output Area (MSOA)
- **Study period:** 2020–2025

## Research Questions

This study addresses four sequential research questions:

1. **RQ1 — Spatial distribution:** Is the spatial distribution of cafés in Greater London non-random? What spatial patterns can be identified, and how did they change between 2020 and 2025?
2. **RQ2 — Associated factors and spatial dependence:** Which neighbourhood-level characteristics are significantly associated with café density at the MSOA level? Do the OLS residuals show evidence of spatial dependence?
3. **RQ3 — Business dynamics prediction:** Can café survival, new openings and MSOA-level growth be predicted from observed urban socioeconomic and spatial characteristics?
4. **RQ4 — POI semantic embeddings:** Do POI-based semantic embeddings provide additional predictive value beyond conventional structured variables?

## Data Sources

| Data category | Main source | Role in the study |
|---|---|---|
| Café and other POIs | Historical OpenStreetMap extracts provided by Geofabrik | Café locations, density, business-dynamics labels, local competition and POI documents |
| Café validation data | Food Hygiene Rating Scheme | External validation of the 2025 OSM café extraction |
| Demographic characteristics | 2021 Census, Office for National Statistics | Age structure, tenure and household composition |
| Population | ONS mid-2022 population estimates | MSOA-level population density |
| Deprivation | Index of Multiple Deprivation 2019, London Datastore | Neighbourhood deprivation |
| Public transport accessibility | Transport for London PTAL data | Public transport accessibility |
| Station locations | Transport for London and Department for Transport | Distance to the nearest station |
| Housing market | ONS median house price data | Neighbourhood housing-market characteristics |
| Boundaries and lookup tables | ONS Open Geography Portal and London Datastore | Spatial aggregation and geographical conversion |

The analysis uses 2021 MSOA boundaries for both study years to maintain a consistent spatial framework. Some historical variables required geographical conversion or reconstruction. In particular, 2015 PTAL was used as a proxy for accessibility in the 2020 model.

## Outcome and Explanatory Variables

The project constructed several analytical datasets for different research tasks.

### MSOA-level café density

Café density was calculated for each MSOA. Because its distribution was strongly right-skewed and included zero-count areas, the dependent variable used in the OLS models was:

```text
log(café density + 1)
```

### Café-level business dynamics

Two café-level datasets were constructed:

- **Survival dataset:** Uses cafés and neighbourhood characteristics observed in 2020 to examine whether a café survived to 2025.
- **New-opening dataset:** Distinguishes cafés appearing between 2020 and 2025 from cafés already present in 2020, using 2025 contextual characteristics.

The survival task is closer to prospective prediction. The new-opening task primarily identifies the characteristics of locations associated with newly opened cafés and should not be interpreted as a strict future forecast.

### MSOA-level growth

MSOA growth was measured using the number of newly opened cafés between 2020 and 2025. Two outcomes were modelled:

- whether an MSOA recorded at least one new opening;
- the log-transformed number of new openings.

### Main explanatory variables

The structured variables included:

- Public Transport Accessibility Level;
- Inner/Outer London classification;
- population density;
- distance to the nearest station;
- whether an MSOA centroid was more than 400 metres from a station;
- average café competition within 300 metres;
- chain café status or MSOA-level chain ratio;
- Index of Multiple Deprivation score;
- median house price;
- private renting rate;
- single-person household rate;
- proportion of residents aged 25–44;
- lone-parent household rate;
- couple-without-children household rate.

## Data Preparation

The main data-processing stages included:

1. extracting café and other POI records from historical OSM data;
2. cleaning and standardising café attributes;
3. spatially joining cafés and contextual data to 2021 MSOAs;
4. assigning points located in small boundary gaps to their nearest MSOA;
5. reconstructing the 2020 station network by removing stations opened after 2020;
6. constructing accessibility, competition, demographic and socioeconomic features;
7. identifying chain cafés using OSM brand tags and standardised name matching;
8. constructing café survival, new-opening and MSOA-growth labels;
9. validating the 2025 OSM café dataset against FHRS records;
10. constructing MSOA-level POI documents for Doc2Vec.

In the external validation, 82.0% of the 2025 OSM cafés were matched to an FHRS record within 50 metres. Among the matched records, 82.2% were classified as high- or medium-quality spatial matches.

## Methodology

### 1. Spatial distribution analysis

Global Moran’s I was used to test whether café density was spatially autocorrelated.

Local Indicators of Spatial Association were then used to identify:

- High–High café-density clusters;
- Low–Low clusters;
- High–Low spatial outliers;
- Low–High spatial outliers.

A Queen-contiguity spatial weights matrix was used in the spatial analysis.

### 2. OLS regression and spatial diagnostics

Separate OLS models were estimated for 2020 and 2025.

Before modelling:

- right-skewed variables were log-transformed where appropriate;
- correlation analysis was conducted;
- Variance Inflation Factors were used to identify multicollinearity;
- the final 10-variable OLS specification had VIF values below five.

The Breusch–Pagan test indicated heteroscedasticity in both years, so HC3 robust standard errors were used.

Spatial dependence was evaluated using:

- Moran’s I for the OLS residuals;
- LM-lag and LM-error tests;
- Robust LM-lag and Robust LM-error tests.

The dissertation used these tests as spatial model-selection diagnostics. It did not proceed to estimate a spatial lag or spatial error regression model.

### 3. Machine-learning models

Three algorithms were compared:

- Logistic Regression;
- Random Forest;
- XGBoost.

The models were applied to:

- café survival;
- café new-opening classification;
- MSOA-level new-opening occurrence;
- MSOA-level new-opening counts.

The survival dataset contained 66.5% surviving cafés and 33.5% closed cafés. Class weights were therefore used to address class imbalance.

Classification performance was evaluated primarily using the Area Under the ROC Curve. MSOA-level count models were evaluated using R².

### 4. POI semantic embeddings

Each MSOA was treated as a document and the functional categories of POIs inside it were treated as words.

A 20-dimensional Doc2Vec representation was trained using the Distributed Memory architecture with:

```text
dm = 1
vector_size = 20
window = 5
min_count = 3
epochs = 100
fixed random seed
```

Two POI-filtering strategies were tested. The resulting embeddings were added to the 14 structured baseline features for the café survival and new-opening tasks.

## Key Findings

### Spatial distribution

The number of cafés recorded in OSM increased from 3,270 in 2020 to 4,562 in 2025, an increase of approximately 39.5%.

However, this change should not be interpreted entirely as genuine business growth. It may also reflect post-pandemic recovery and changes in OSM data completeness.

Café density was significantly spatially clustered in both years:

| Year | Global Moran’s I | Significance |
|---|---:|---:|
| 2020 | 0.6087 | p = 0.001 |
| 2025 | 0.5690 | p = 0.001 |

The overall spatial pattern can be described as **continued central concentration combined with outward expansion**.

Inner London remained the main concentration of cafés, while several Outer London boroughs recorded faster percentage growth from smaller baseline values.

### Factors associated with café density

The OLS models achieved the following explanatory performance:

| Year | R² | Adjusted R² |
|---|---:|---:|
| 2020 | 0.603 | 0.599 |
| 2025 | 0.631 | 0.628 |

PTAL, median house price, the proportion of residents aged 25–44 and the single-person household rate were positively and significantly associated with café density in both years.

Chain ratio was also positively associated with café density, although its coefficient was smaller in 2025.

IMD score, distance to the nearest station and the Inner London dummy were not statistically significant after the other variables were controlled for.

These results indicate conditional associations. They do not show that these characteristics directly caused café concentration.

### Remaining spatial dependence

Significant spatial autocorrelation remained in the OLS residuals:

| Year | Residual Moran’s I | Significance |
|---|---:|---:|
| 2020 | 0.1448 | p = 0.001 |
| 2025 | 0.1190 | p = 0.001 |

The Robust LM-lag test provided stronger support for a spatial lag specification in 2020, while the Robust LM-error test provided stronger support for a spatial error specification in 2025.

These diagnostics suggest that the non-spatial OLS models did not fully capture the spatial structure of café density.

### Machine-learning performance

| Task | Logistic Regression | Random Forest | XGBoost |
|---|---:|---:|---:|
| Café survival — AUC | 0.5175 | 0.5680 | **0.5790** |
| Café new opening — AUC | 0.5962 | **0.7083** | 0.7048 |
| MSOA new-opening occurrence — AUC | 0.7786 | 0.7852 | **0.7881** |
| MSOA new-opening count — R² | 0.336 | **0.347** | 0.313 |

Café survival was the most difficult outcome to predict. This suggests that neighbourhood-level variables do not capture many business-specific factors affecting closure or survival.

New openings were more predictable than survival. The strongest performance was obtained for classifying whether an MSOA experienced at least one new opening.

For the MSOA classification task, Logistic Regression performed close to Random Forest and XGBoost. The small numerical differences indicate that additional model complexity produced limited gains for this outcome.

PTAL and average café competition within 300 metres repeatedly appeared among the more important predictors in the better-performing growth models. These should be interpreted as predictive features rather than causal drivers.

### Additional value of Doc2Vec

| Task | Structured baseline | Doc2Vec version 1 | Doc2Vec version 2 |
|---|---:|---:|---:|
| Café survival — AUC | **0.5790** | 0.5700 | 0.5717 |
| Café new opening — AUC | **0.7083** | 0.7077 | 0.7062 |

Neither Doc2Vec version produced a numerical improvement over the structured baseline.

Several embedding dimensions still received relatively high feature-importance scores, particularly in the new-opening models. However, high feature importance did not translate into improved overall discrimination.

Because the differences were small and were not tested for statistical significance, the appropriate conclusion is:

> Under the spatial scale, feature construction and model settings used in this study, no additional predictive improvement from the POI embeddings was observed.

This should not be interpreted as evidence that Doc2Vec universally reduces prediction performance.

## Research Contributions

The study makes three main contributions:

1. It provides an empirical analysis of changes in London’s café geography between 2020 and 2025.
2. It combines spatial statistics, regression diagnostics and machine-learning prediction within a common urban analytical framework.
3. It tests whether a more complex POI representation adds predictive value beyond structured urban indicators and demonstrates that greater representational complexity does not necessarily improve prediction.

## Limitations

The main limitations are:

- Only two time points were analysed, making it difficult to separate long-term growth, post-pandemic recovery and changes in OSM completeness.
- The 2020 analysis uses 2015 PTAL as an accessibility proxy.
- Several demographic and socioeconomic variables use the same cross-sectional values in the 2020 and 2025 models.
- Both years use 2021 MSOA boundaries, and geographical conversion may introduce uncertainty.
- Individual business characteristics such as rent, store size, footfall, operating quality and management were unavailable.
- Doc2Vec embeddings were constructed at the MSOA level, so all cafés within the same MSOA shared the same representation.
- The models identify associations and predictive relationships rather than causal effects.
- Spatial regression model selection was diagnosed, but spatial lag and spatial error models were not estimated.
- Numerical differences between machine-learning models were not formally tested for statistical significance.

## Repository Structure (temporary)

```text
.
├── README.md
├── README_CN.md
├── notebooks/
│   ├── 01_osm_cafe_preparation.ipynb
│   ├── 02_feature_engineering.ipynb
│   ├── 03_spatial_autocorrelation.ipynb
│   ├── 04_ols_and_spatial_diagnostics.ipynb
│   ├── 05_ml_business_dynamics.ipynb
│   └── 06_doc2vec_comparison.ipynb
├── src/
│   ├── data_processing.py
│   ├── spatial_analysis.py
│   ├── modelling.py
│   └── visualisation.py
├── data/
│   ├── README.md
│   ├── raw/
│   ├── interim/
│   └── sample/
├── figures/
│   ├── spatial_distribution_2020_2025.png
│   ├── lisa_clusters_2020_2025.png
│   ├── ols_coefficients.png
│   └── model_auc_comparison.png
├── docs/
│   ├── dissertation.pdf
│   ├── data_sources.md
│   ├── data_dictionary.md
│   └── methodology_notes.md
├── requirements.txt
├── LICENSE
└── .gitignore
```

The filenames above describe the intended repository organisation and should be adjusted to match the final cleaned files.

The notebooks should document the research process and remain readable from start to finish. Reusable and stable functions should be moved into `src/` rather than duplicated across notebooks.

## Data Availability

Large raw and intermediate datasets are not intended to be stored directly in this Git repository.

The future `data/README.md` should document:

- dataset name and provider;
- official download page;
- reference year;
- download date;
- spatial and temporal coverage;
- original licence;
- local filename;
- processing steps;
- whether redistribution is permitted.

Small, non-sensitive sample data may be placed in `data/sample/` to demonstrate the expected data structure.

OpenStreetMap-derived data must comply with the Open Database Licence and its attribution requirements. ONS, TfL, GLA, DfT and FHRS materials remain subject to their respective licensing terms.

## Licence

The code licence has not yet been finalised.

Any future code licence will apply only to original code in this repository. It will not automatically alter the rights associated with third-party datasets, basemaps, external figures or the dissertation text.

## Citation

If you refer to this research, please use:

```text
Ren, Y. (2026). Spatial Distribution, Associated Factors and Business Dynamics
of the Café Industry in London (2020–2025): Integrating Spatial Analysis and
Machine Learning. MSc Dissertation, University College London.
```

Repository URL and access date should be added after publication.

## Author

**Yanxi Ren**  
MSc Urban Spatial Science  
University College London

- GitHub: https://github.com/REN2001318
- Email: 18636912568@163.com
