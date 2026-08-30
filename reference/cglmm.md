# Fit cosinor model with `{glmmTMB}`

Given an outcome and time variable, fit the cosinor model with optional
covariate effects.

## Usage

``` r
cglmm(
  formula,
  data,
  family = stats::gaussian(),
  quietly = TRUE,
  dispformula = ~1,
  ziformula = ~0,
  ...
)
```

## Arguments

- formula:

  A `formula` specifying the cosinor model to be fit. The cosinor
  portion of the formula is controlled by including
  [`amp_acro()`](https://docs.ropensci.org/GLMMcosinor/reference/amp_acro.md)
  on the right hand side of the formula. See
  [`amp_acro`](https://docs.ropensci.org/GLMMcosinor/reference/amp_acro.md)
  for more details.

- data:

  A `data.frame` containing the variables used in the model.

- family:

  A `family` function or a character string naming a family function.
  See [`?family`](https://rdrr.io/r/stats/family.html) and
  [`?glmmTMB::family_glmmTMB`](https://rdrr.io/pkg/glmmTMB/man/nbinom2.html)
  for options.

- quietly:

  A `logical`. If `TRUE`, shows warning messages when wrangling data and
  fitting model. Defaults to `TRUE`.

- dispformula:

  A one-sided (i.e., no response variable) `formula` for dispersion
  combining fixed and random effects, including cosinor components using
  [`amp_acro()`](https://docs.ropensci.org/GLMMcosinor/reference/amp_acro.md).
  Defaults to `~1`.

- ziformula:

  A one-sided (i.e., no response variable) `formula` for zero-inflation
  combining fixed and random effects, including cosinor components using
  [`amp_acro()`](https://docs.ropensci.org/GLMMcosinor/reference/amp_acro.md).
  Defaults to `~0`.

- ...:

  Optional additional arguments passed to
  [`glmmTMB::glmmTMB()`](https://rdrr.io/pkg/glmmTMB/man/glmmTMB.html).

## Value

Returns a fitted cosinor model as a `cglmm` object.

## References

Tong, YL. Parameter Estimation in Studying Circadian Rhythms, Biometrics
(1976). 32(1):85–94.

## Examples

``` r
# Single component cosinor model
cglmm(
  vit_d ~ amp_acro(time_col = time, group = "X", period = 12),
  data = vitamind
)
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
#> (Intercept)       5.02730
#> group1           -3.02708
#> group0:main_rrr1  1.11543
#> group1:main_rrr1  1.06721
#> group0:main_sss1  1.69253
#> group1:main_sss1  1.59953
#> group0:main_rrr2  0.19996
#> group1:main_rrr2  0.15810
#> group0:main_sss2  1.04920
#> group1:main_sss2  0.95925
#> 
#>  Transformed Coefficients: 
#>                Estimate
#> (Intercept)     5.02730
#> [group=1]      -3.02708
#> [group=0]:amp1  2.02703
#> [group=1]:amp1  1.92287
#> [group=0]:amp2  1.06808
#> [group=1]:amp2  0.97219
#> [group=0]:acr1  0.98810
#> [group=1]:acr1  0.98242
#> [group=0]:acr2  1.38247
#> [group=1]:acr2  1.40744
```
