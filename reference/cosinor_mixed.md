# cosinor_mixed dataset for cosinor modeling examples.

Simulated data set to illustrate a mixed cosinor model. The `Y` column
contains a simulated outcome variable that varies over the time variable
(`times`). The `subject` column is a grouping variable that can be used
as a random effect. The rhythm has a period of 24 hours. Data was
simulated using `simulate_cosinor`.

## Usage

``` r
cosinor_mixed
```

## Format

A `data.frame` with 3 variables: `Y`, `times`, and `subject`.
