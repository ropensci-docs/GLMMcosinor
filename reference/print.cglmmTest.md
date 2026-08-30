# Print results of test of cosinor model

Print results of test of cosinor model

## Usage

``` r
# S3 method for class 'cglmmTest'
print(x, ...)
```

## Arguments

- x:

  A `test_cosinor` object.

- ...:

  Arguments passed to `print`

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
test_cosinor_levels(
  mod_2_component,
  param = "amp",
  x_str = "group"
)
#> Test Details: 
#> Parameter being tested:
#> Amplitude
#> 
#> Comparison type:
#> levels
#> 
#> Grouping variable used for comparison between groups: group
#> Reference group: 0
#> Comparator group: 1
#> 
#> cglmm model has2 components. Component 1 is being used for comparison between groups.
#> 
#> 
#> 
#> Global test: 
#> Statistic: 
#> 117.64
#> 
#> P-value: 
#> 0
#> 
#> 
#> Individual tests:
#> Statistic: 
#> 10.85
#> 
#> P-value: 
#> 0
#> 
#> Estimate and 95% confidence interval:
#> 1.01 (0.83 to 1.2)
```
