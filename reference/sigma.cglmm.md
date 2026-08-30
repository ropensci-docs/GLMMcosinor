# Extract residual standard deviation or dispersion parameter

see [`?glmmTMB::sigma`](https://rdrr.io/r/stats/sigma.html) for more
details.

## Usage

``` r
# S3 method for class 'cglmm'
sigma(object, ...)
```

## Arguments

- object:

  An object of class `cglmm`.

- ...:

  (ignored; for method compatibility)

## Value

a `numeric`.

## Examples

``` r
testdata_poisson <- simulate_cosinor(100,
  n_period = 2,
  mesor = 7,
  amp = c(0.1, 0.5),
  acro = c(1, 1),
  beta.mesor = 4.4,
  beta.amp = c(0.1, 0.46),
  beta.acro = c(0.5, -1.5),
  family = "poisson",
  period = c(12, 6),
  n_components = 2,
  beta.group = TRUE
)

mod <- cosinor_model <- cglmm(
  Y ~ group + amp_acro(times,
    period = c(12, 6),
    n_components = 2,
    group = "group"
  ),
  data = testdata_poisson,
  family = glmmTMB::nbinom1()
)
sigma(mod)
#> [1] 0.06137209
```
