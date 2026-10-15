# Print a brief summary of the `cglmm` model.

Print a brief summary of the `cglmm` model.

## Usage

``` r
# S3 method for class 'cglmm'
print(x, digits = getOption("digits"), ...)
```

## Arguments

- x:

  A `cglmm` object.

- digits:

  Controls the number of digits displayed in the summary output.

- ...:

  Additional, ignored arguments.

## Value

`print(x)` returns `x` invisibly.

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
```
