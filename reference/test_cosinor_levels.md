# Test for differences in a cosinor model between levels of the grouping variable.

Given a time variable and optional covariates, generate inference a
cosinor fit. For the covariate named (or vector of covariates), this
function performs a Wald test comparing the group with covariates equal
to 1 to the group with covariates equal to 0. This may not be the
desired result for continuous covariates.

## Usage

``` r
test_cosinor_levels(
  x,
  x_str,
  param = "amp",
  comparison_A,
  comparison_B,
  component_index = 1,
  ci_level = 0.95
)
```

## Arguments

- x:

  A `cglmm` object.

- x_str:

  A `character`. The name of the grouping variable within which
  differences in the selected cosinor characteristic (amplitude or
  acrophase) will be tested.

- param:

  A `character`. Either `"amp"` or `"acr"` for testing differences in
  amplitude or acrophase, respectively.

- comparison_A:

  An `integer, or string`. Refers to the first level within the grouping
  variable `x_str` that is to act as the reference group in the
  comparison. Ensure that it corresponds to the name of the level in the
  original dataset.

- comparison_B:

  An `integer, or string`. Refers to the second level within the
  grouping variable `x_str` that is to act as the comparator group in
  the comparison. Ensure that it corresponds to the name of the level in
  the original dataset.

- component_index:

  An `integer`. If `comparison_type = "levels"`, `component_index`
  indicates which component is being compared between the levels of the
  grouping variable.

- ci_level:

  The level for calculated confidence intervals. Defaults to `0.95`.

## Value

Returns a `test_cosinor` object.

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
test_cosinor_levels(mod_2_component, param = "amp", x_str = "group")
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
#> 124.11
#> 
#> P-value: 
#> 0
#> 
#> 
#> Individual tests:
#> Statistic: 
#> 11.14
#> 
#> P-value: 
#> 0
#> 
#> Estimate and 95% confidence interval:
#> 1.02 (0.84 to 1.2)
```
