# Fit the cosinor GLMM model using the output from `update_formula_and_data()` and a new formula

Fit the cosinor GLMM model using the output from
[`update_formula_and_data()`](https://docs.ropensci.org/GLMMcosinor/reference/update_formula_and_data.md)
and a new formula

## Usage

``` r
fit_model_and_process(obj, formula, ...)
```

## Arguments

- obj:

  Output from
  [`update_formula_and_data()`](https://docs.ropensci.org/GLMMcosinor/reference/update_formula_and_data.md).

- formula:

  A (optionally) new formula to use when fitting the cosinor model
  (maybe with random effects) or other covariates found in the data.

- ...:

  Optional additional arguments passed to
  [`glmmTMB::glmmTMB()`](https://rdrr.io/pkg/glmmTMB/man/glmmTMB.html).

## Value

Returns a fitted cosinor model as a `cglmm` object.

## Examples

``` r
# Use vitamind data but add a "patient" identifier used as a random effect
vitamind2 <- vitamind
vitamind2$patient <- sample(
  LETTERS[1:5],
  size = nrow(vitamind2), replace = TRUE
)

# Use update_formula_and_data() to perform wrangling steps of cglmm()
# without yet fitting the model
data_and_formula <- update_formula_and_data(
  data = vitamind2,
  formula = vit_d ~ X + amp_acro(time,
    group = "X",
    period = 12
  )
)

# print formula from above
data_and_formula$newformula
#> vit_d ~ X + X:main_rrr1 + X:main_sss1
#> <environment: 0x55614c82f348>

# fit model while adding random effect to cosinor model formula.
mod <- fit_model_and_process(
  obj = data_and_formula,
  formula = update.formula(
    data_and_formula$newformula, . ~ . + (1 | patient)
  )
)

mod
#> 
#>  Conditional Model 
#> 
#>  Raw formula: 
#> vit_d ~ X + (1 | patient) + X:main_rrr1 + X:main_sss1 
#> 
#>  Raw Coefficients: 
#>              Estimate
#> (Intercept)  29.72050
#> X1            1.85902
#> X0:main_rrr1  0.94592
#> X1:main_rrr1  6.56966
#> X0:main_sss1  6.07604
#> X1:main_sss1  4.78863
#> 
#>  Transformed Coefficients: 
#>             Estimate
#> (Intercept) 29.72050
#> [X=1]        1.85902
#> [X=0]:amp    6.14923
#> [X=1]:amp    8.12967
#> [X=0]:acr    1.41636
#> [X=1]:acr    0.62986
mod$fit # printing the `glmmTMB` model within shows Std.Dev. of random effect
#> Formula:          vit_d ~ X + (1 | patient) + X:main_rrr1 + X:main_sss1
#> Zero inflation:         ~1 - 1
#> Data: newdata
#>       AIC       BIC    logLik -2*log(L)  df.resid 
#> 1247.3630 1273.7496 -615.6815 1231.3630       192 
#> Random-effects (co)variances:
#> 
#> Conditional model:
#>  Groups   Name        Std.Dev.
#>  patient  (Intercept) 0.6644  
#>  Residual             5.2239  
#> 
#> Number of obs: 200 / Conditional model: patient, 5
#> 
#> Dispersion estimate for gaussian family (sigma^2): 27.3 
#> 
#> Fixed Effects:
#> 
#> Conditional model:
#>  (Intercept)            X1  X0:main_rrr1  X1:main_rrr1  X0:main_sss1  
#>      29.7205        1.8590        0.9459        6.5697        6.0760  
#> X1:main_sss1  
#>       4.7886  
```
