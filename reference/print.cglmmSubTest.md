# Print test of model

Print test of model

## Usage

``` r
# S3 method for class 'cglmmSubTest'
print(x, ...)
```

## Arguments

- x:

  A `sub_test_cosinor` object.

- ...:

  Additional, ignored arguments.

## Value

`print(x)` returns `x` invisibly.

## Examples

``` r
data_2_component <- simulate_cosinor(
  n = 10000,
  mesor = 5,
  amp = c(2, 5),
  acro = c(0, pi),
  beta.mesor = 4,
  beta.amp = c(3, 4),
  beta.acro = c(0, pi / 2),
  family = "gaussian",
  n_components = 2,
  period = c(10, 12),
  beta.group = TRUE
)
mod_2_component <- cglmm(
  Y ~ group + amp_acro(times,
    n_components = 2, group = "group",
    period = c(10, 12)
  ),
  data = data_2_component
)
test_output <- test_cosinor_levels(
  mod_2_component,
  param = "amp",
  x_str = "group"
)
print(test_output$global.test)
#> Statistic: 
#> 130.68
#> 
#> P-value: 
#> 0
```
