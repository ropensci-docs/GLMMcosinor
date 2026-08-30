# Used to specify a cosinor component in the model formula.

Checks the validity of user inputs before creating an updated formula
and associated modifications to the `data.frame`.

## Usage

``` r
amp_acro(time_col, n_components = 1, group, period, ...)
```

## Arguments

- time_col:

  A `numeric` column within the
  [`data.frame()`](https://rdrr.io/r/base/data.frame.html) passed by via
  the `data` arg containing the time values.

- n_components:

  The Number of cosinor components in the model.

- group:

  A vector of the names for the group factors (column names within the
  [`data.frame()`](https://rdrr.io/r/base/data.frame.html) passed by via
  the `data` arg).

- period:

  A `numeric` value or vector containing the period. The number of
  values should be equal to `n_components`.

- ...:

  Extra arguments for use within `GLMMcosinor`.

## Value

A `data.frame` and `formula` appropriate for use by `data_processor()`.

## Examples

``` r
# Single component cosinor model
cglmm(
  vit_d ~ amp_acro(time_col = time, group = "X", period = 12),
  data = vitamind
)
#> Warning: the ‘findbars’ function has moved to the reformulas package. Please update your imports, or ask an upstream package maintainer to do so.
#> Warning: the ‘nobars’ function has moved to the reformulas package. Please update your imports, or ask an upstream package maintainer to do so.
#> 
#>  Conditional Model 
#> 
#>  Raw formula: 
#> vit_d ~ X:main_rrr1 + X:main_sss1 
#> 
#>  Raw Coefficients: 
#>              Estimate
#> (Intercept)  30.32687
#> X0:main_rrr1  0.86521
#> X1:main_rrr1  6.47628
#> X0:main_sss1  6.24437
#> X1:main_sss1  4.66703
#> 
#>  Transformed Coefficients: 
#>             Estimate
#> (Intercept) 30.32687
#> [X=0]:amp    6.30403
#> [X=1]:amp    7.98270
#> [X=0]:acr    1.43311
#> [X=1]:acr    0.62444

# 2-component cosinor model with simulated data
sim_data <- simulate_cosinor(
  n = 500,
  mesor = 5,
  amp = c(2, 1),
  acro = c(1, 1.5),
  beta.mesor = 2,
  beta.amp = c(2, 1),
  beta.acro = c(1, 1.5),
  family = "gaussian",
  period = c(12, 6),
  n_components = 2,
  beta.group = TRUE,
)

cglmm(
  Y ~ group + amp_acro(times,
    n_components = 2,
    group = "group",
    period = c(12, 6)
  ),
  data = sim_data,
  family = gaussian
)
#> 
#>  Conditional Model 
#> 
#>  Raw formula: 
#> Y ~ group + group:main_rrr1 + group:main_sss1 + group:main_rrr2 +      group:main_sss2 
#> 
#>  Raw Coefficients: 
#>                  Estimate
#> (Intercept)       4.95470
#> group1           -2.99887
#> group0:main_rrr1  1.13994
#> group1:main_rrr1  1.15148
#> group0:main_sss1  1.65901
#> group1:main_sss1  1.60876
#> group0:main_rrr2  0.09234
#> group1:main_rrr2  0.05761
#> group0:main_sss2  1.02093
#> group1:main_sss2  1.09658
#> 
#>  Transformed Coefficients: 
#>                Estimate
#> (Intercept)     4.95470
#> [group=1]      -2.99887
#> [group=0]:amp1  2.01290
#> [group=1]:amp1  1.97839
#> [group=0]:amp2  1.02510
#> [group=1]:amp2  1.09809
#> [group=0]:acr1  0.96877
#> [group=1]:acr1  0.94957
#> [group=0]:acr2  1.48059
#> [group=1]:acr2  1.51831
```
