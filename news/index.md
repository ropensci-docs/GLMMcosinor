# Changelog

## GLMMcosinor (development version)

- Refactor polar_plot() and autoplot() to avoid functions that are
  defined within other functions. Separate out into separate scripts
  where necessary.

- Add example of using likelihood-ratio test to assess differential
  rhythmicity.

- Use the mean of continuous variables as the reference level, and the
  first level for factors when creating newdata within
  [`autoplot()`](https://ggplot2.tidyverse.org/reference/autoplot.html).

- Fix test-coverage GHA.

## GLMMcosinor 0.2.1

CRAN release: 2024-10-31

- Fixed issues [\#14](https://github.com/ropensci/GLMMcosinor/issues/14)
  and [\#15](https://github.com/ropensci/GLMMcosinor/issues/15) relating
  to handling model formulas without
  [`amp_acro()`](https://docs.ropensci.org/GLMMcosinor/reference/amp_acro.md)
  components.

- Update import from glmmTMB which previously failed with released
  version.

- Perform the glmmTMB’s `.onLoad()` when GLMMcosinor is loaded by
  including an `@importFrom` glmmTMB in pkg documentation.

- Plot labels now correspond to the name of the group(s), and they are
  more concise.

- Component labels have been removed from
  [`polar_plot()`](https://docs.ropensci.org/GLMMcosinor/reference/polar_plot.md)
  when there is only one component in the model.

## GLMMcosinor 0.2.0

CRAN release: 2024-01-11

- Successful peer review from rOpenSci!

- Create a [`sigma()`](https://rdrr.io/r/stats/sigma.html) function
  which gets the dispersion parameter from a given `cglmm` model (by
  calling [`glmmTMB::sigma()`](https://rdrr.io/r/stats/sigma.html)).

- Improve introduction to cosinor modeling in the getting-started
  vignette.

- Fix small typos in vignettes/docs and make plot legend title
  consistent across
  [`autoplot()`](https://ggplot2.tidyverse.org/reference/autoplot.html)
  and
  [`polar_plot()`](https://docs.ropensci.org/GLMMcosinor/reference/polar_plot.md).

- Rename functions to be `snake_case` and all S3 class names to
  `camelCase`.

## GLMMcosinor 0.1.0

- First development version of
  [GLMMcosinor](https://docs.ropensci.org/GLMMcosinor) for submission to
  rOpenSci.

- Includes functions for fitting a cosinor model, similarly to the
  {cosinor} R package but using the
  [glmmTMB](https://github.com/glmmTMB/glmmTMB) modeling framework to
  allow more flexibility in terms of fitting **generalized** linear
  **mixed** cosinor models.
