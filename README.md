# Missing-data-mice-imputation

An R Markdown practical comparing ad hoc and multiple imputation methods for handling missing data, using the `mice` package on the `College` dataset from `ISLR`.

## Overview

This repository contains a single R Markdown document that simulates missingness under a **Seen Data Dependent (SDD / MAR)** mechanism and compares five approaches to handling it:

- **Complete data** — the benchmark regression fit before any missingness is introduced
- **Listwise deletion** — dropping any row with a missing value
- **Regression imputation** — replacing missing values with their predicted value from a regression model (no added noise)
- **Stochastic regression imputation** — same as above, but with random residual noise added to each prediction
- **Multiple imputation** — generating 5 imputed datasets using Predictive Mean Matching (`method = "pmm"`), fitting the regression on each, and pooling the results with Rubin's rules

The goal is to show how each method affects the estimated linear relationship between `Outstate` (out-of-state tuition) and `Expend` (instructional expenditure per student), and to illustrate why multiple imputation is generally the most reliable approach when missingness is not simply MCAR.

## Contents

- Data processing — selecting `Outstate`, `PhD`, `Terminal`, and `Expend` from the `College` dataset
- Fitting the benchmark regression on the complete data
- Generating missingness in `Expend` and `Terminal`, driven by `Outstate` and `PhD` respectively (SDD/MAR)
- Inspecting the missingness pattern with `ggmice::plot_pattern()`
- Ad hoc imputation methods: listwise deletion, regression imputation, stochastic regression imputation
- Multiple imputation with `mice`, including the predictor matrix and pooled regression results
- A final comparison table of coefficients and standard errors across all five methods

## Requirements

- R (≥ 4.0 recommended)
- `tidyverse`
- `mice`
- `ggmice`
- `ISLR`

Install with:

```r
install.packages(c("tidyverse", "mice", "ggmice", "ISLR"))
```

To knit to PDF, a LaTeX distribution (e.g. TinyTeX) is also required:

```r
install.packages("tinytex")
tinytex::install_tinytex()
```

## Usage

1. Clone the repo.
2. Open the `.Rmd` file in RStudio.
3. Knit to PDF or HTML. `set.seed(123)` is used throughout, so results are reproducible.

## Key result

| Method | Coefficient (`Outstate`) | Std. Error |
|---|---|---|
| Complete data (truth) | 0.8732 | 0.0345 |
| Listwise deletion | 0.7021 | 0.0415 |
| Regression imputation | 0.7035 | 0.0178 |
| Stochastic regression imputation | 0.7149 | 0.0257 |
| **Multiple imputation** | **0.8533** | 0.0443 |

Under this SDD/MAR mechanism, multiple imputation produces the coefficient closest to the true, complete-data value, and is the only method whose standard error appropriately reflects the added uncertainty from the missing data rather than artificially shrinking it. The three ad hoc methods all produce biased coefficients, and the two imputation-based ad hoc methods additionally understate their standard errors.
