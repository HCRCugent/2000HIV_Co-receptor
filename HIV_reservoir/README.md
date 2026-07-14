# 2000HIV Co-receptor Reservoir Analysis

This repository contains the R Markdown workflow used to compare HIV reservoir size and transcriptional activity between people living with HIV harbouring CXCR4-using (X4), CCR5-using (R5), and mixed-tropic viruses in the 2000HIV cohort.

The main script, **2000HIV_Coreceptor_reservoir.Rmd**, performs cohort preparation, non-parametric and parametric statistical analyses, and generates publication-ready figures and summary tables for reservoir-related measurements.

## Analysis overview

The workflow includes:

### Cohort preparation

- Imports participant-level reservoir and clinical data.
- Excludes persistent elite controllers (`CONTROLLER_1B == "EC_persistent"`).
- Classifies participants according to HIV-1 co-receptor tropism (R5, X4, and Mixed).
- Defines multiple analysis subsets:
  - Full cohort
  - Participants with a known infection date within one year
  - Participants initiating ART within three years of infection
  - Participants diagnosed after 2014

### Reservoir analysis

Compares the following HIV reservoir measurements between tropism groups:

- Total HIV DNA
- Intact HIV reservoir
- Defective HIV reservoir
- Intact-to-total reservoir ratio
- Cell-associated long LTR RNA
- Cell-associated POL RNA

### Primary statistical analysis

- Performs Kruskal–Wallis tests for overall group comparisons.
- Performs Dunn's post hoc tests with Benjamini–Hochberg multiple-testing correction.
- Reports medians, interquartile ranges, sample sizes, and adjusted p-values.

### Parametric sensitivity analysis

- Applies log2(x + 1) transformation to copy-number measurements.
- Applies arcsine square-root transformation to proportional measurements.
- Performs one-way ANOVA followed by Tukey HSD multiple comparisons.
- Reports transformed group means, confidence intervals, p-values, and model assumption checks (Shapiro–Wilk and Bartlett tests).

### Data visualisation

- Generates publication-ready violin plots with boxplots and individual observations.
- Annotates plots with Benjamini–Hochberg-adjusted Dunn test p-values.
- Produces figures separately for each reservoir marker and analysis subset.

### Export of results

- Exports primary statistical results to Excel.
- Exports sensitivity-analysis results to Excel.
- Saves publication-quality PDF figures for all analyses.

## Repository structure

```
├── 2000HIV_Coreceptor_reservoir.Rmd      # Main analysis workflow
└── README.md                             # Repository documentation
```

The participant-level dataset is not included because it contains sensitive research data.

## Input data

The script expects one Excel workbook containing participant-level clinical and reservoir measurements. At minimum, the dataset should contain the variables referenced throughout the analysis, including:

- Tropism
- CONTROLLER_1B
- INF_date_known_1y
- TIME_NEG_TO_ART
- HIV_DATE
- Total_million_avg
- Rainbow_DSI
- Defective_Rainbow
- intact_total_ratio
- longLTR_cp_Mio
- POL_cp_Mio

Additional variables may be required if the workflow is extended or customised.

## Output

The workflow generates:

- PDF figures for each reservoir marker and analysis subset, the figures can later be edited in the Adobe illustrator.
- Excel tables containing Kruskal–Wallis and Dunn test results.
- Excel tables containing ANOVA and Tukey HSD sensitivity analyses.

## Data availability

The individual-level 2000HIV clinical and reservoir dataset is not distributed in this repository. Access is subject to the cohort governance procedures, participant consent, institutional approvals, and applicable data-protection requirements.