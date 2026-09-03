# 2000HIV Olink Proteomics Co-receptor Analysis

This repository contains the R Markdown workflow used to identify differentially expressed proteins (DEPs) between PLWH harbouring CXCR4-using (X4) and CCR5-using (R5) viruses in the 2000HIV cohort.

The main script, **2000HIV_Olink_Coreceptor.Rmd**, performs data integration, covariate assessment, principal component analysis (PCA), covariate correction, differential protein expression testing using limma, targeted visualisation of pre-selected immune proteins, and generation of publication-ready figures and summary tables. Confounder identification is conducted separately and its outputs are assumed as input to this workflow.

---

## Analysis overview

### Cohort preparation

- Imports Olink NPX matrix (post-bridging normalisation and QC), the 2000HIV clinical dataset, and the tropism classification file.
- Merges datasets using participant identifiers.
- Excludes persistent elite controllers (`CONTROLLER_1B == "EC_persistent"`).
- Retains participants classified as R5 or X4 only.
- Sets R5 as the reference category for all regression analyses.
- Computes seasonal covariates (sine and cosine terms of days since 1 January 2019) to model continuous seasonal variation.
- Creates ethnicity dummy variables (Asian, Black, Hispanic, Mixed, White) and a binary recruitment centre indicator (CENTER_RUMC).
- Handles −Inf values by converting them to NA.
- Defines complete cohort, Discovery, and Validation subsets.

### Genetic principal components

- Imports externally computed genetic PCs derived from genome-wide SNP data after imputation and QC.
- Merges the first five genetic PCs into the analysis dataset by participant ID for use as covariates to control for population stratification.

### Principal component analysis (PCA)

- Performs PCA on the full Olink protein matrix (discovery cohort).
- Calculates the proportion of variance explained by each principal component.
- Evaluates associations between principal components and biological or technical covariates using univariate linear regression and adjusted R² heatmaps, before and after covariate correction.
- Assesses regression model assumptions for each PC-covariate pair, including normality of residuals (Q-Q plots), homoscedasticity (Breusch-Pagan test and residuals-versus-fitted plots), and linearity for continuous covariates (natural cubic spline versus linear fit by ANOVA).
- Produces a correlation heatmap across covariates to identify collinearity; PANDEMIC_BEFOREAFTER was dropped from the final model due to correlation exceeding 0.5 with seasonal terms.

### Covariate correction

- Fits per-protein linear regression models regressing out predefined confounders.
- Generates covariate-corrected residuals for PCA-based quality assessment.

### Differential protein expression (DEP) analysis

- Fits multivariable linear regression models for each Olink protein using limma with empirical Bayes moderation.
- Compares X4 versus R5 while adjusting for: age, sex at birth, BMI, smoking status, seasonality (sine and cosine), genetic PC1, time to blood processing, pre-ART CD4:CD8 ratio, and recruitment centre.
- CD4CD8_PRECART missing values (n = 258) are imputed using k-nearest neighbours (k = 5) prior to modelling, to avoid excluding approximately 17% of participants for a single covariate.
- Applies Benjamini-Hochberg multiple-testing correction across all proteins.
- Performs analyses in the complete cohort, Discovery cohort, and Validation cohort independently.
- Exports results to Excel with protein name, panel, log fold change, t-statistic, raw p-value, and adjusted p-value.

### Cross-cohort comparison

- Merges Discovery and Validation DEP results on protein name.
- Classifies proteins into six groups based on directional consistency and significance: significant in both cohorts with consistent direction, significant in both with opposite direction, nominally significant in discovery with consistent direction in validation, discovery-only, validation-only, and not significant.
- Produces a biplot of log fold change (discovery x-axis versus validation y-axis) with the top 20 proteins from the primary category labelled.

### Targeted protein visualisation

- Visualises raw NPX values for 15 pre-selected immune-relevant proteins: CCL5, CD14, CD163, CXCL10, CXCL9, GZMB, IFNG, IL1B, IL6, OSM, TNF, CCL17, CCL22, IL10, TGFB1.
- Generates violin plots with overlaid jitter and boxplots, stratified by tropism and cohort.
- Tests pairwise differences using Wilcoxon rank-sum test (primary) and Welch two-sample t-test (sensitivity analysis), both with global Benjamini-Hochberg correction across proteins within each cohort.
- Exports summary statistics (medians, IQR, means, SD) and test results to Excel.

### Data visualisation

- PCA scatter plots before and after covariate correction, coloured by tropism.
- Scree plots showing variance explained per component.
- Adjusted R² heatmaps of covariate associations with PCs.
- Correlation heatmaps across covariates.
- Volcano plots for discovery and validation DEP results.
- Biplot of fold change concordance across cohorts.
- Violin plots for targeted protein comparisons.

---

## Repository structure

```
├── 2000HIV_Olink_Coreceptor.Rmd    # Main analysis workflow
└── README.md                       # Repository documentation
```

Participant-level datasets are not included as they contain sensitive research data.

---

## Input data

The script expects three input datasets:

### 1. Olink NPX matrix
Post-bridging normalisation and QC. Proteins occupy columns from `ADA_Inflammation` to `ZPR1_Neurology_II`. Each protein column is named `ProteinSymbol_PanelName`.

Required variables:
- `ID`
- All Olink NPX protein columns

### 2. Clinical dataset

Required variables:
- `ID`
- `Tropism` (R5 / X4)
- `CONTROLLER_1B`
- `AGE`
- `SEX_BIRTH`
- `BMI_BASELINE`
- `SMOKING_CURRENT`
- `DATE_VISIT`
- `CENTER`
- `ETHNICITY`
- `PANDEMIC_BEFOREAFTER`
- `COVID19`
- `COVID_VACC`
- `TIMETOLAB`
- `CD4_NADIR`
- `CD4CD8_PRECART`
- `VL_ZENITH`
- `VL_PRECART`
- `HEPC`, `HEPA_BASELINE`, `HEPB_BASELINE`
- `CART_DURATION`
- `HIV_STAGE_CDC`
- `EARLY_CART`
- `HIV_DURATION`
- `COHORT` (DISCOVERY / VALIDATION)

### 3. Genetic principal components

Required variables:
- `ID`
- `PC1` through `PC5` (minimum; script uses PC1 in the final limma model)

---

## Output

The workflow generates the following outputs, all saved to `ManuscriptTropism/`:

| File | Content |
|------|---------|
| `Olink_PCA_Scree_Plot_before_correction.pdf/.tiff` | Scree plot before covariate correction |
| `Olink_PCA_before_correction.pdf/.tiff` | PCA scatter plot before correction |
| `PCA_Regression_Heatmap_before_correction.pdf/.tiff` | Adjusted R² heatmap before correction |
| `Olink_PCA_Scree_Plot_after_correction.pdf/.tiff` | Scree plot after covariate correction |
| `Olink_PCA_after_correction.pdf/.tiff` | PCA scatter plot after correction |
| `PCA_Regression_Heatmap_after_correction.pdf/.tiff` | Adjusted R² heatmap after correction |
| `Correlation_Matrix_Plot.pdf` | Covariate correlation heatmap |
| `PCA_Regression_Assumption_Checks.xlsx` | Breusch-Pagan and spline linearity test results |
| `PCA_Regression_QQ_Plots_Normality_PC1_to_PC5.pdf/.tiff` | Q-Q plots of residuals |
| `PCA_Regression_Residuals_vs_Fitted_Homoscedasticity_PC1_to_PC5.pdf/.tiff` | Residuals versus fitted plots |
| `PCA_Regression_Linearity_Continuous_Covariates_PC1_to_PC5.pdf/.tiff` | Linearity plots for continuous covariates |
| `Significant_Proteins_DiscoveryCohort.xlsx` | limma DEP results, discovery cohort |
| `Significant_Proteins_ValidationCohort.xlsx` | limma DEP results, validation cohort |
| `Volcano_Plot_PValue_DiscoveryCohort.pdf` | Volcano plot (raw p), discovery |
| `Volcano_Plot_AdjPValue_DiscoveryCohort.pdf` | Volcano plot (adjusted p), discovery |
| `Volcano_Plot_PValue_ValidationCohort.pdf` | Volcano plot (raw p), validation |
| `Volcano_Plot_AdjPValue_ValidationCohort.pdf` | Volcano plot (adjusted p), validation |
| `Biplot_logFC_Disc_vs_Val_DISC_SameDir_top30.pdf` | Fold change concordance biplot |
| `RAW_NPX_R5vsX4_Medians_and_Pairwise.xlsx` | Wilcoxon results for targeted proteins |
| `RAW_NPX_R5vsX4_WelchTtest_GlobalBH.xlsx` | Welch t-test sensitivity results |
| `Plots_NPX_R5vsX4_Discovery.pdf` | Violin plots, discovery cohort |
| `Plots_NPX_R5vsX4_Validation.pdf` | Violin plots, validation cohort |
| `Plots_NPX_R5vsX4_BothCohorts.pdf` | Violin plots, both cohorts combined |
| `Plots_NPX_R5vsX4_Discovery_WelchTtest.pdf` | Violin plots (Welch), discovery |
| `Plots_NPX_R5vsX4_Validation_WelchTtest.pdf` | Violin plots (Welch), validation |
| `Plots_NPX_R5vsX4_BothCohorts_WelchTtest.pdf` | Violin plots (Welch), both cohorts |

---

## Statistical methods

### Principal component analysis

- PCA on Olink NPX matrix using `FactoMineR::PCA`.
- Covariate association analysis using univariate linear regression; adjusted R² reported.
- Assumption testing: Breusch-Pagan test for homoscedasticity; ANOVA comparing linear versus natural cubic spline fit for linearity of continuous covariates.

### Differential protein expression

- Limma linear model with empirical Bayes moderation (`lmFit`, `eBayes`, `topTable`).
- Contrast: X4 versus R5 (log fold change reported as X4 minus R5).
- Covariates in final model: AGE, SEX_BIRTH, BMI_BASELINE, SMOKING_CURRENT, season_sin, season_cos, PC1, TIMETOLAB, CD4CD8_PRECART, CENTER_RUMC.
- Multiple-testing correction: Benjamini-Hochberg across all proteins.

### Targeted protein tests

- Primary: Wilcoxon rank-sum test with global Benjamini-Hochberg correction across the 15 target proteins within each cohort.
- Sensitivity: Welch two-sample t-test with the same correction scheme.

---

## Data availability

The individual-level 2000HIV Olink, clinical, and genetic datasets are not distributed in this repository. Access is subject to the cohort governance procedures, participant consent, institutional approvals, and applicable data-protection requirements.
