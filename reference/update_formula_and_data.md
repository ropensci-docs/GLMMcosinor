# Update data and formula for fitting cglmm model

Update data and formula for fitting cglmm model

## Usage

``` r
update_formula_and_data(
  data,
  formula,
  family = "gaussian",
  quietly = TRUE,
  dispformula = ~1,
  ziformula = ~0
)
```

## Arguments

- data:

  input data for fitting cglmm model.

- formula:

  model formula, specified by user including
  [`amp_acro()`](https://docs.ropensci.org/GLMMcosinor/reference/amp_acro.md).

- family:

  the model family.

- quietly:

  controls whether messages from amp_acro are displayed. TRUE by default

- dispformula:

  The formula specifying the dispersion model

- ziformula:

  The formula specifying the zero-inflation model

## Value

Returns a `list`.

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
#> <environment: 0x556159edc068>

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
#> (Intercept)  29.64388
#> X1            1.93606
#> X0:main_rrr1  0.96588
#> X1:main_rrr1  6.46825
#> X0:main_sss1  6.18892
#> X1:main_sss1  4.82437
#> 
#>  Transformed Coefficients: 
#>             Estimate
#> (Intercept) 29.64388
#> [X=1]        1.93606
#> [X=0]:amp    6.26384
#> [X=1]:amp    8.06925
#> [X=0]:acr    1.41598
#> [X=1]:acr    0.64084
mod$fit # printing the `glmmTMB` model within shows Std.Dev. of random effect
#> Formula:          vit_d ~ X + (1 | patient) + X:main_rrr1 + X:main_sss1
#> Zero inflation:         ~1 - 1
#> Data: newdata
#>       AIC       BIC    logLik -2*log(L)  df.resid 
#>  1247.800  1274.187  -615.900  1231.800       192 
#> Random-effects (co)variances:
#> 
#> Conditional model:
#>  Groups   Name        Std.Dev.
#>  patient  (Intercept) 0.4868  
#>  Residual             5.2429  
#> 
#> Number of obs: 200 / Conditional model: patient, 5
#> 
#> Dispersion estimate for gaussian family (sigma^2): 27.5 
#> 
#> Fixed Effects:
#> 
#> Conditional model:
#>  (Intercept)            X1  X0:main_rrr1  X1:main_rrr1  X0:main_sss1  
#>      29.6439        1.9361        0.9659        6.4682        6.1889  
#> X1:main_sss1  
#>       4.8244  
```
