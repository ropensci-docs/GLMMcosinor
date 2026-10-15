# Vitamin D dataset for cosinor modeling examples.

Simulated data set to illustrate the cosinor model. The `vit_d` column
contains the blood vitamin D levels which vary over time (`time`). The
rhythm of the vitamind D fluctuations follows a cosine function and can
be modeled with a cosinor model. The `X` column is a binary covariate
representing two groups of patients and is associated with the
characteristics of the rhythm. The rhythm has a period of about 12
hours.

## Usage

``` r
vitamind
```

## Format

A `data.frame` with 3 variables: `vit_d`, `time`, and `X`.
