# 2000HIV Co-receptor Flow Cytometry Analysis

This repository contains the R Markdown workflow used to compare peripheral blood flow cytometry phenotypes between people living with HIV harbouring CXCR4-using (X4) and CCR5-using (R5) viruses in the 2000HIV cohort.

The main script, **2000HIV_flow_Coreceptor_Percentage.Rmd**, performs data preprocessing, covariate assessment, principal component analysis (PCA), covariate correction, differential cell population analysis, and generation of publication-ready figures and summary tables.

## Analysis overview

The workflow includes:

### Cohort preparation

- Imports flow cytometry, clinical, and genetic principal component datasets.
- Merges datasets using participant identifiers.
- Excludes persistent elite controllers (`CONTROLLER_1B == "EC_persistent"`).
- Retains participants classified as R5 or X4.
- Sets R5 as the reference category for all regression analyses.
- Computes seasonal covariates using visit date.
- Creates ethnicity and study-centre indicator variables.
- Defines Discovery and Validation cohorts.

### Flow cytometry preprocessing

- Standardises flow marker names.
- Creates a mapping between original and simplified marker names.
- Removes participants with missing covariate information.
- Prepares marker abundance data for downstream analyses.

### Principal component analysis (PCA)

- Performs PCA on flow cytometry measurements.
- Calculates the proportion of variance explained by each principal component.
- Evaluates associations between principal components and biological or technical covariates.
- Produces adjusted R² heatmaps before and after covariate correction.
- Assesses model assumptions, including homoscedasticity and linearity.

### Covariate correction

- Fits linear regression models for each flow cytometry marker.
- Adjusts for predefined biological and technical covariates.
- Generates covariate-corrected residuals for downstream analyses.

### Differential cell population analysis

- Fits multivariable linear regression models for each flow cytometry population.
- Compares X4 and R5 participants while adjusting for predefined covariates.
- Reports regression coefficients, confidence intervals, raw p-values, and Benjamini–Hochberg adjusted p-values.
- Performs analyses separately in the Discovery and Validation cohorts.

### Data visualisation

- Generates PCA plots.
- Produces variance-explained plots.
- Creates adjusted R² heatmaps.
- Generates volcano plots summarising differential flow cytometry populations.
- Produces publication-ready figures for manuscript preparation.

### Export of results

- Exports differential analysis results to Excel.
- Exports PCA summaries.
- Saves publication-quality figures.

---

## Repository structure

```
├── 2000HIV_flow_Coreceptor_Percentage.Rmd    # Main analysis workflow
└── README.md                                 # Repository documentation
```

The participant-level datasets are not included because they contain sensitive research data.

---

## Input data

The script expects three input datasets:

1. Flow cytometry percentage data
2. Clinical dataset
3. Genetic principal component (PCA) dataset

At minimum, the datasets should contain the variables referenced throughout the analysis, including:

### Flow cytometry

- ID
- Flow cytometry population percentages

### Clinical data

- ID
- Tropism
- CONTROLLER_1B
- AGE
- SEX_BIRTH
- CD4_NADIR
- TIMETOLAB
- DATE_VISIT
- CENTER
- ETHNICITY
- COVID_VACC
- COHORT

### Genetic data

- ID
- Genetic principal components used for covariate adjustment

---

## Output

The workflow generates:

- PCA summaries
- Covariate-adjusted R² heatmaps
- Differential flow cytometry analysis tables
- Volcano plots
- Publication-ready figures
- Excel files containing regression coefficients, confidence intervals, and adjusted p-values

---

## Statistical methods

### Principal component analysis

- Principal component analysis (PCA)
- Covariate association analysis using linear regression
- Adjusted R² calculation
- Assumption testing (homoscedasticity and linearity)

### Differential analysis

- Multivariable linear regression
- Adjustment for predefined biological and technical covariates
- Benjamini–Hochberg multiple-testing correction

---

## Data availability

The individual-level 2000HIV flow cytometry, clinical, and genetic datasets are not distributed in this repository. Access is subject to the cohort governance procedures, participant consent, institutional approvals, and applicable data-protection requirements.# 2000HIV_Kavita