# Summarize a cosinor model Given a time variable and optional covariates, generate inference a cosinor fit. Gives estimates, confidence intervals, and tests for the raw parameters, and for the mean, amplitude, and acrophase parameters. If the model includes covariates, the function returns the estimates of the mean, amplitude, and acrophase for the group with covariates equal to 1 and equal to 0. This may not be the desired result for continuous covariates.

Summarize a cosinor model Given a time variable and optional covariates,
generate inference a cosinor fit. Gives estimates, confidence intervals,
and tests for the raw parameters, and for the mean, amplitude, and
acrophase parameters. If the model includes covariates, the function
returns the estimates of the mean, amplitude, and acrophase for the
group with covariates equal to 1 and equal to 0. This may not be the
desired result for continuous covariates.

## Usage

``` r
# S3 method for class 'cglmm'
summary(object, ci_level = 0.95, ...)
```

## Arguments

- object:

  An object of class `cglmm`

- ci_level:

  The level for calculated confidence intervals. Defaults to 0.95.

- ...:

  Currently unused

## Value

Returns a summary of the `cglmm` model as a `cglmmSummary` object.

## Examples

``` r


fit <- cglmm(vit_d ~ X + amp_acro(time,
  group = "X",
  n_components = 1,
  period = 12
), data = vitamind)
summary(fit)
#> 
#>  Conditional Model 
#> Raw model coefficients:
#>                estimate standard.error   lower.CI upper.CI    p.value    
#> (Intercept)  29.6897986      0.4583696 28.7914106 30.58819 < 2.22e-16 ***
#> X1            1.9018605      0.7919688  0.3496302  3.45409   0.016331 *  
#> X0:main_rrr1  0.9307837      0.6260656 -0.2962822  2.15785   0.137089    
#> X1:main_rrr1  6.5102912      0.9303406  4.6868572  8.33373 2.6010e-12 ***
#> X0:main_sss1  6.2009927      0.6701952  4.8874342  7.51455 < 2.22e-16 ***
#> X1:main_sss1  4.8184563      0.8963299  3.0616821  6.57523 7.6259e-08 ***
#> ---
#> Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
#> 
#> Transformed coefficients:
#>                estimate standard.error    lower.CI upper.CI    p.value    
#> (Intercept) 29.68979858     0.45836964 28.79141059 30.58819 < 2.22e-16 ***
#> [X=1]        1.90186054     0.79196879  0.34963023  3.45409   0.016331 *  
#> [X=0]:amp1   6.27046001     0.66965643  4.95795753  7.58296 < 2.22e-16 ***
#> [X=1]:amp1   8.09946996     0.89570579  6.34391887  9.85502 < 2.22e-16 ***
#> [X=0]:acr1   1.42180626     0.09993555  1.22593618  1.61768 < 2.22e-16 ***
#> [X=1]:acr1   0.63715378     0.11493856  0.41187833  0.86243  2.966e-08 ***
#> ---
#> Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
