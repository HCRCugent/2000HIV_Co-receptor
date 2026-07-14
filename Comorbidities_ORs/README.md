# 2000HIV Co-receptor Clinical Outcome Analysis

This repository contains the R Markdown workflow used to compare clinical characteristics and binary clinical outcomes between people living with HIV harbouring **CXCR4-using (X4)** and **CCR5-using (R5)** viruses in the 2000HIV cohort.

The main script, `2000HIV_Coreceptor_Forestplot_ORs.Rmd`, performs covariate assessment, logistic regression, sensitivity analyses, prevalence summaries, and generation of publication-ready forest plots.

## Analysis overview

The workflow includes:

1. **Cohort preparation**
   - Retains participants classified as R5 or X4 for the main regression analyses.
   - Excludes persistent elite controllers (`CONTROLLER_1B == "EC_persistent"`).
   - Sets R5 as the reference category, so reported odds ratios represent **X4 versus R5**.
   - Creates derived variables for virological responder groups, CDC stage 3, and hepatic steatosis.

2. **Covariate correlation analysis**
   - Calculates pairwise Spearman correlations among selected clinical covariates.
   - Uses pairwise-complete observations.
   - Applies Benjamini–Hochberg correction to unique variable pairs.
   - Produces a correlation heatmap and Excel tables containing correlation coefficients, raw p-values, and adjusted p-values.

3. **Exploratory screening of variables**
   - Fits logistic regression models for binary variables and linear regression models for continuous variables.
   - Runs both unadjusted models and adjusted models.

4. **Selected-outcome logistic regression**
   - Fits an unadjusted and adjusted model for each outcome
   - Reports odds ratios, 95% confidence intervals, p-values, sample sizes, and event counts for R5 and X4 groups.
   - Generates combined forest plots comparing unadjusted and adjusted estimates.

5. **Sensitivity analysis including CD4 nadir**
   - Repeats the selected-outcome analyses with CD4 nadir included in the relevant adjusted models.
   - Evaluates whether associations are attenuated after accounting for historical immunological disease severity.

6. **Model diagnostics and interaction analyses**
   - Assesses the linearity of continuous predictors in the logit using natural cubic splines and likelihood-ratio tests.
   - Tests `Tropism × CD4_NADIR` interactions using nested logistic regression models.
   - Exports interaction results and explicit model formulas.

7. **Restricted-cohort and follow-up analyses**
   - Performs analyses in participants with a known infection date and in post-2014 subsets.
   - Evaluates selected outcomes measured during two years of follow-up.
   - Produces supplementary forest plots with a common odds-ratio scale.

8. **Outcome prevalence summaries**
   - Summarises event counts and percentages for R5, X4, and mixed-tropism groups.
   - Produces an Excel summary and a grouped bar plot.

## Repository structure

```text
├── 2000HIV_Coreceptor_Forestplot_ORs.Rmd   # Main analysis workflow
└── README.md                               # Repository documentation
```

The clinical input dataset is not included because it contains participant-level research data.


## Input data

The script expects one Excel workbook containing participant-level clinical data. At minimum, the dataset must contain the variables referenced in the analysis, including:

- `Tropism`
- `CONTROLLER_1B`
- `AGE`
- `SEX_BIRTH`
- `TIMETOCART`
- `CD4_NADIR`
- `BMI_BASELINE`
- `SMOKING_CURRENT`
- the clinical outcome variables defined in the `var_covs` and `outcomes` objects

Binary outcomes should be coded consistently as 0/1 or as two-level variables that can be converted to 0/1. The script includes a helper function, `coerce_binary01()`, for common numeric, logical, factor, and character formats. Coding should nevertheless be checked before running the models because the event level for an unordered two-level character variable is determined from its sorted levels.


## Data availability

The individual-level 2000HIV clinical dataset is not distributed in this repository. Access is subject to the cohort governance procedures, participant consent, institutional approvals, and applicable data-protection requirements.

