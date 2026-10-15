# Model specification

``` r

library(GLMMcosinor)
library(dplyr)
```

## `cglmm()`

[`cglmm()`](https://docs.ropensci.org/GLMMcosinor/reference/cglmm.md)
wrangles the data appropriately to fit the cosinor model given the
formula specified by the user. It provides estimates of amplitude,
acrophase, and MESOR (Midline Statistic Of Rhythm).

The formula argument for
[`cglmm()`](https://docs.ropensci.org/GLMMcosinor/reference/cglmm.md) is
specified using the [lme4](https://github.com/lme4/lme4/) style (for
details see
[`vignette("lmer", package = "lme4")`](https://cran.rstudio.com/web/packages/lme4/vignettes/lmer.pdf)).
The only difference is that it allows for use of an
[`amp_acro()`](https://docs.ropensci.org/GLMMcosinor/reference/amp_acro.md)
call within the formula that is used to identify the circadian
components and relevant variables in the `data.frame`. Any other
combination of covariates can also be included in the formula as well as
random effects and zero-inflation (`ziformula`) and dispersion
(`dispformula`) formulae. For detailed examples of how to specify
models, see the
[mixed-models](https://docs.ropensci.org/GLMMcosinor/articles/mixed-models.html),
[model-specification](https://docs.ropensci.org/GLMMcosinor/articles/model-specification.html)
and
[multiple-components](https://docs.ropensci.org/GLMMcosinor/articles/multiple-components.html)
vignettes.

## Using cglmm()

The following examples use data simulated by the the `simulate_cosinor`
function.

### Specifying a single-component model with no grouping variable

Here, we fit a simple cosinor model to “testdata_simple” - simulated
data from a Poisson distribution loaded in this vignette. In this
example, there is no grouping variable.

``` r

testdata_simple <- simulate_cosinor(
  1000,
  n_period = 2,
  mesor = 5,
  amp = 2,
  acro = 1,
  beta.mesor = 4,
  beta.amp = 1,
  beta.acro = 0.5,
  family = "poisson",
  period = c(12),
  n_components = 1,
  beta.group = TRUE
)
```

``` r

object <- cglmm(
  Y ~ amp_acro(times,
    period = 12
  ),
  data = filter(testdata_simple, group == 0),
  family = poisson()
)
object
#> 
#>  Conditional Model 
#> 
#>  Raw formula: 
#> Y ~ main_rrr1 + main_sss1 
#> 
#>  Raw Coefficients: 
#>             Estimate
#> (Intercept)  4.99845
#> main_rrr1    1.08228
#> main_sss1    1.68235
#> 
#>  Transformed Coefficients: 
#>             Estimate
#> (Intercept)  4.99845
#> amp          2.00041
#> acr          0.99913
```

The output shows the estimates for the raw coefficients in addition to
the transformed estimates for amplitude (amp) and acrophase (acr) and
MESOR (`(Intercept)`). The previous section of this vignette: [An
overview of the statistical methods used for parameter
estimation](https://docs.ropensci.org/GLMMcosinor/articles/GLMMcosinor.html#an-overview-of-the-statistical-methods-used-for-parameter-estimation)
outlines the difference between the raw coefficients and the transformed
coefficients.

We would interpret the output as follows:

- `MESOR estimate = 4.99845`

- `Amplitude estimate = 1.08228`

- `Acrophase estimate = 0.99913`

Note that this estimate is in radians to align with conventions. To
interpret this, we can express `0.99913 radians` as a fraction of the
total 2\pi and multiply by the period to get the time when the response
is a maximal. Hence, \frac{0.99913}{2\pi} \times 12 = 1.908 in the units
of the `time_col` column in the original dataframe. This is saying that
the peak response would occur after 1.908 time-units and every 12
time-units after this. We can confirm this by plotting:

``` r

autoplot(object, superimpose.data = TRUE)
```

![](model-specification_files/figure-html/unnamed-chunk-5-1.png)

### Specifying a single-component model with a grouping variable and a shared MESOR

Now, we can add a grouping variable by adding the name of the group in
the
[`amp_acro()`](https://docs.ropensci.org/GLMMcosinor/reference/amp_acro.md)
function:

``` r

testdata_simple_gaussian <- simulate_cosinor(
  1000,
  n_period = 2,
  mesor = 5,
  amp = 2,
  acro = 1,
  beta.mesor = 4,
  beta.amp = 1,
  beta.acro = 0.5,
  family = "gaussian",
  period = c(12),
  n_components = 1,
  beta.group = TRUE
)
```

``` r

object <- cglmm(
  Y ~ amp_acro(times,
    period = 12,
    group = "group"
  ),
  data = testdata_simple_gaussian,
  family = gaussian
)
object
#> 
#>  Conditional Model 
#> 
#>  Raw formula: 
#> Y ~ group:main_rrr1 + group:main_sss1 
#> 
#>  Raw Coefficients: 
#>                  Estimate
#> (Intercept)       4.47411
#> group0:main_rrr1  1.03269
#> group1:main_rrr1  0.90209
#> group0:main_sss1  1.67745
#> group1:main_sss1  0.48497
#> 
#>  Transformed Coefficients: 
#>               Estimate
#> (Intercept)    4.47411
#> [group=0]:amp  1.96984
#> [group=1]:amp  1.02419
#> [group=0]:acr  1.01896
#> [group=1]:acr  0.49328
```

In the example above, the amplitude and phase are being estimated
separately for the two groups but the **intercept term is shared**. This
represents a shared estimate of the MESOR for both groups and is useful
if two groups are known to have a common baseline (or equilibrium
point). Hence, we would interpret the transformed coefficients as
follows:

- The MESOR estimate is 4.47411 for both `group = 0` and `group = 1`.

- The estimates for amplitude and acrophase are with reference to the a
  MESOR estimate of 4.47411

``` r

autoplot(object)
```

![](model-specification_files/figure-html/unnamed-chunk-8-1.png)

However, the groups in this dataset were simulated with two different
MESORs, and so it would be more appropriate to specify an intercept term
in the formula, as this will estimate the MESOR for both `group = 0` and
`group = 1`:

### Specifying a single-component model with a grouping variable and an intercept (MESOR)

Similarly to a normal regression model with
[lme4](https://github.com/lme4/lme4/) or
[glmmTMB](https://github.com/glmmTMB/glmmTMB), we can add a term for the
group in the model so that we can estimate the **difference in MESOR**
between the two groups.

``` r

object <- cglmm(
  Y ~ group + amp_acro(times,
    period = 12,
    group = "group"
  ),
  data = testdata_simple_gaussian,
  family = gaussian()
)
object
#> 
#>  Conditional Model 
#> 
#>  Raw formula: 
#> Y ~ group + group:main_rrr1 + group:main_sss1 
#> 
#>  Raw Coefficients: 
#>                  Estimate
#> (Intercept)       4.96475
#> group1           -0.98130
#> group0:main_rrr1  1.04667
#> group1:main_rrr1  0.88812
#> group0:main_sss1  1.68266
#> group1:main_sss1  0.47976
#> 
#>  Transformed Coefficients: 
#>               Estimate
#> (Intercept)    4.96475
#> [group=1]     -0.98130
#> [group=0]:amp  1.98163
#> [group=1]:amp  1.00942
#> [group=0]:acr  1.01433
#> [group=1]:acr  0.49528
```

This is the same dataset used in the previous example, but note the
following differences:

- The MESOR estimate for the reference group (`group = 0`) is given by
  `(Intercept) = 4.96476`

- The estimate for the difference between the MESOR of the reference
  group (`group = 0`) and the treatment group (`group = 1`) is given by
  `[group=1] = -0.98129`. As such, the estimate for the MESOR of
  `group = 1` is `3.98347`.

- The estimates for amplitude and acrophase are slightly different to
  the previous example because there is no longer a shared MESOR.

Plotting this model and comparing to the previous model which used the
same dataset, one can appreciate the importance of specifying the
formula correctly in order to gain the most accurate model.

``` r

autoplot(object)
```

![](model-specification_files/figure-html/unnamed-chunk-10-1.png)

We may also be interested in estimating the MESOR for the two groups
separately, rather than the difference between groups. To achieve this,
we can remove the intercept term by using `0 +`.

``` r

cglmm(
  Y ~ 0 + group + amp_acro(times,
    period = 12,
    group = "group"
  ),
  data = testdata_simple,
  family = poisson()
)
#> 
#>  Conditional Model 
#> 
#>  Raw formula: 
#> Y ~ group + group:main_rrr1 + group:main_sss1 - 1 
#> 
#>  Raw Coefficients: 
#>                  Estimate
#> group0            4.99845
#> group1            3.99631
#> group0:main_rrr1  1.08228
#> group1:main_rrr1  0.87665
#> group0:main_sss1  1.68235
#> group1:main_sss1  0.48195
#> 
#>  Transformed Coefficients: 
#>               Estimate
#> [group=0]      4.99845
#> [group=1]      3.99631
#> [group=0]:amp  2.00041
#> [group=1]:amp  1.00040
#> [group=0]:acr  0.99914
#> [group=1]:acr  0.50266
```

### Specifying more complicated models using the `amp_acro()` function

The
[`amp_acro()`](https://docs.ropensci.org/GLMMcosinor/reference/amp_acro.md)
function controls the cosinor components of model (specifically, this
affects just the fixed-effects part). It provides the user with the
ability to specify grouping structures, the period of the rhythm, and
the number of components. There are several arguments that the user must
specify:

- `group` is the name of the grouping variable in the dataset. This can
  be a string or an object

- `time_col` is the name of the time column in the dataset. This can be
  a string or an object

- `n_components` is the number of components.

If the user wishes to fit a multicomponent cosinor model, they can
specify the number of components using the `n_components` variable. The
value of `n_components` will need to match the length of the `group` and
`period` arguments as these will be combined for each component.

For example:

``` r

testdata_two_components <- simulate_cosinor(
  1000,
  n_period = 10,
  mesor = 7,
  amp = c(0.1, 0.4),
  acro = c(1, 1.5),
  beta.mesor = 4.4,
  beta.amp = c(2, 1),
  beta.acro = c(1, -1.5),
  family = "poisson",
  period = c(12, 6),
  n_components = 2,
  beta.group = TRUE
)
```

``` r

cglmm(
  Y ~ group + amp_acro(
    time_col = times,
    n_components = 2,
    period = c(12, 6),
    group = c("group", "group")
  ),
  data = testdata_two_components,
  family = poisson()
)
#> 
#>  Conditional Model 
#> 
#>  Raw formula: 
#> Y ~ group + group:main_rrr1 + group:main_sss1 + group:main_rrr2 +      group:main_sss2 
#> 
#>  Raw Coefficients: 
#>                  Estimate
#> (Intercept)       7.00043
#> group1           -2.60739
#> group0:main_rrr1  0.05665
#> group1:main_rrr1  1.08271
#> group0:main_sss1  0.08378
#> group1:main_sss1  1.68926
#> group0:main_rrr2  0.02884
#> group1:main_rrr2  0.07367
#> group0:main_sss2  0.39671
#> group1:main_sss2 -0.99891
#> 
#>  Transformed Coefficients: 
#>                Estimate
#> (Intercept)     7.00043
#> [group=1]      -2.60739
#> [group=0]:amp1  0.10113
#> [group=1]:amp1  2.00645
#> [group=0]:amp2  0.39776
#> [group=1]:amp2  1.00162
#> [group=0]:acr1  0.97624
#> [group=1]:acr1  1.00082
#> [group=0]:acr2  1.49824
#> [group=1]:acr2 -1.49718
```

In the output, the suffix on the estimates for amplitude and acrophase
represents its component:

- `[group=0]:amp1 = 0.10113` represents the estimate for amplitude of
  `group 0` for the first component

- `[group=1]:amp1 = 2.00645` represents the estimate for amplitude of
  `group 1` for the first component

- `[group=0]:amp2 = 0.39776` represents the estimate for amplitude of
  `group 0` for the second component

- `[group=1]:amp2 = 1.00162` represents the estimate for amplitude of
  `group 1` for the second component

- *Similarly for acrophase estimates*

If a multicomponent model has one component that is grouped with other
components that aren’t, the vector input for `group` must still be the
same length as `n_components` but have the non-grouped components
represented as `group = NA`.

For example, if we wanted only the first component to have a grouped
component, we would specify the `group` argument as
`group = c("group", NA))`.

For a detailed explanation of how to specify multi-component models, see
[multiple-components](https://docs.ropensci.org/GLMMcosinor/articles/multiple-components.html)

### Dispersion and zero-inflation model specification

The
[`cglmm()`](https://docs.ropensci.org/GLMMcosinor/reference/cglmm.md)
function allows users to specify formulas for dispersion and
zero-inflation models. These formulas are independent of the main
formula specification:

``` r

testdata_disp_zi <- simulate_cosinor(1000,
  n_period = 6,
  mesor = 7,
  amp = c(0.1, 0.4, 0.5),
  acro = c(1, 1.5, 0.1),
  beta.mesor = 4.4,
  beta.amp = c(2, 1, 0.4),
  beta.acro = c(1, -1.5, -1),
  family = "gaussian",
  period = c(12, 6, 8),
  n_components = 3
)
object_disp_zi <- cglmm(
  Y ~ group + amp_acro(times,
    n_components = 3,
    period = c(12, 6, 8),
    group = "group"
  ),
  data = testdata_disp_zi, family = gaussian(),
  dispformula = ~ group + amp_acro(times,
    n_components = 2,
    group = "group",
    period = c(12, 6)
  ),
  ziformula = ~ group + amp_acro(times,
    n_components = 3,
    group = "group",
    period = c(7, 8, 2)
  )
)

object_disp_zi
#> 
#>  Conditional Model 
#> 
#>  Raw formula: 
#> Y ~ group + group:main_rrr1 + group:main_sss1 + group:main_rrr2 +      group:main_sss2 + group:main_rrr3 + group:main_sss3 
#> 
#>  Raw Coefficients: 
#>                  Estimate
#> (Intercept)       6.95937
#> group1           -2.54022
#> group0:main_rrr1  0.04699
#> group1:main_rrr1  1.08129
#> group0:main_sss1  0.11911
#> group1:main_sss1  1.65334
#> group0:main_rrr2  0.03439
#> group1:main_rrr2  0.03916
#> group0:main_sss2  0.36981
#> group1:main_sss2 -0.97296
#> group0:main_rrr3  0.50496
#> group1:main_rrr3  0.20937
#> group0:main_sss3  0.11201
#> group1:main_sss3 -0.34949
#> 
#>  Transformed Coefficients: 
#>                Estimate
#> (Intercept)     6.95937
#> [group=1]      -2.54022
#> [group=0]:amp1  0.12804
#> [group=1]:amp1  1.97553
#> [group=0]:amp2  0.37141
#> [group=1]:amp2  0.97374
#> [group=0]:amp3  0.51723
#> [group=1]:amp3  0.40740
#> [group=0]:acr1  1.19502
#> [group=1]:acr1  0.99161
#> [group=0]:acr2  1.47806
#> [group=1]:acr2 -1.53057
#> [group=0]:acr3  0.21828
#> [group=1]:acr3 -1.03106
#> 
#> ***********************
#> 
#>  Dispersion Model 
#> 
#>  Raw  Formula: 
#> ~group + group:disp_rrr1 + group:disp_sss1 + group:disp_rrr2 +      group:disp_sss2 
#> 
#>  Raw  Coefficients: 
#>                  Estimate
#> (Intercept)      -0.03473
#> group1            0.00801
#> group0:disp_rrr1 -0.06511
#> group1:disp_rrr1  0.01818
#> group0:disp_sss1  0.04175
#> group1:disp_sss1  0.00363
#> group0:disp_rrr2  0.00043
#> group1:disp_rrr2  0.02406
#> group0:disp_sss2  0.03210
#> group1:disp_sss2  0.01321
#> 
#>  Transformed  Coefficients: 
#>                Estimate
#> (Intercept)    -0.03473
#> [group=1]       0.00801
#> [group=0]:amp1  0.07735
#> [group=1]:amp1  0.01854
#> [group=0]:amp2  0.03210
#> [group=1]:amp2  0.02745
#> [group=0]:acr1  2.57140
#> [group=1]:acr1  0.19700
#> [group=0]:acr2  1.55754
#> [group=1]:acr2  0.50203
#> 
#> ***********************
#> 
#>  Zero-Inflation Model 
#> 
#>  Raw  Formula: 
#> ~group + group:zi_rrr1 + group:zi_sss1 + group:zi_rrr2 + group:zi_sss2 +      group:zi_rrr3 + group:zi_sss3 
#> 
#>  Raw  Coefficients: 
#>                 Estimate
#> (Intercept)    -22.30760
#> group1          -1.97191
#> group0:zi_rrr1  -0.02439
#> group1:zi_rrr1  -0.01372
#> group0:zi_sss1   0.01177
#> group1:zi_sss1   0.00752
#> group0:zi_rrr2   0.00693
#> group1:zi_rrr2   0.00262
#> group0:zi_sss2   0.06000
#> group1:zi_sss2   0.03145
#> group0:zi_rrr3   0.00287
#> group1:zi_rrr3   0.00175
#> group0:zi_sss3   0.02895
#> group1:zi_sss3   0.01434
#> 
#>  Transformed  Coefficients: 
#>                 Estimate
#> (Intercept)    -22.30760
#> [group=1]       -1.97191
#> [group=0]:amp1   0.02708
#> [group=1]:amp1   0.01565
#> [group=0]:amp2   0.06040
#> [group=1]:amp2   0.03156
#> [group=0]:amp3   0.02909
#> [group=1]:amp3   0.01445
#> [group=0]:acr1   2.69181
#> [group=1]:acr1   2.63995
#> [group=0]:acr2   1.45577
#> [group=1]:acr2   1.48769
#> [group=0]:acr3   1.47184
#> [group=1]:acr3   1.44937
```

The output provides estimates for conditional model (default model), the
dispersion model, and also the zero-inflation model. By default,
`dispformula = ~1`, and `ziformula = ~0` which means these additional
models will not be generated in the output.

*Note that in the example above, the value for the periods and the
number of components in the dispersion and zero-inflation formulas were
chosen arbitrarily and purely for demonstration.*

## Using `summary(cglmm)`

The [`summary()`](https://rdrr.io/r/base/summary.html) method for
`cglmm` objects provides a more detailed summary of the model and its
parameter estimates and uncertainty. It outputs the estimates, standard
errors, confidence intervals, and p-values for both the raw model
parameters and the transformed parameters. The summary statistics do not
represent a comparison between any groups for the cosinor components -
that is the role of the `test_cosinor()` function.

Here is an example of how to use
[`summary()`](https://rdrr.io/r/base/summary.html):

``` r

object <- cglmm(
  Y ~ group + amp_acro(times,
    period = 12,
    group = "group"
  ),
  data = testdata_simple,
  family = poisson()
)
summary(object)
#> 
#>  Conditional Model 
#> Raw model coefficients:
#>                      estimate standard.error     lower.CI upper.CI    p.value
#> (Intercept)       4.998454142    0.003463730  4.991665356  5.00524 < 2.22e-16
#> group1           -1.002150001    0.005937109 -1.013786521 -0.99051 < 2.22e-16
#> group0:main_rrr1  1.082281784    0.003347565  1.075720677  1.08884 < 2.22e-16
#> group1:main_rrr1  0.876651963    0.006198710  0.864502714  0.88880 < 2.22e-16
#> group0:main_sss1  1.682350718    0.003919418  1.674668800  1.69003 < 2.22e-16
#> group1:main_sss1  0.481951763    0.005936670  0.470316104  0.49359 < 2.22e-16
#>                     
#> (Intercept)      ***
#> group1           ***
#> group0:main_rrr1 ***
#> group1:main_rrr1 ***
#> group0:main_sss1 ***
#> group1:main_sss1 ***
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> Transformed coefficients:
#>                    estimate standard.error     lower.CI upper.CI    p.value    
#> (Intercept)     4.998454142    0.003463730  4.991665356  5.00524 < 2.22e-16 ***
#> [group=1]      -1.002150001    0.005937109 -1.013786521 -0.99051 < 2.22e-16 ***
#> [group=0]:amp1  2.000409408    0.004275553  1.992029478  2.00879 < 2.22e-16 ***
#> [group=1]:amp1  1.000398004    0.006379631  0.987894156  1.01290 < 2.22e-16 ***
#> [group=0]:acr1  0.999134804    0.001439122  0.996314177  1.00196 < 2.22e-16 ***
#> [group=1]:acr1  0.502662067    0.005739524  0.491412807  0.51391 < 2.22e-16 ***
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

The summary statistics for dispersion and zero-inflation models will
also be provided by the
[`summary()`](https://rdrr.io/r/base/summary.html) function, if the
original `cglmm` object being analyzed contains them. The following
demonstration uses the model specified in the **Dispersion and
Zero-inflation model specification** section of this vignette:

``` r

summary(object_disp_zi)
#> 
#>  Conditional Model 
#> Raw model coefficients:
#>                     estimate standard.error    lower.CI upper.CI    p.value    
#> (Intercept)       6.95936537     0.03071384  6.89916735  7.01956 < 2.22e-16 ***
#> group1           -2.54021516     0.04358864 -2.62564733 -2.45478 < 2.22e-16 ***
#> group0:main_rrr1  0.04698989     0.04402192 -0.03929148  0.13327  0.2857821    
#> group1:main_rrr1  1.08128651     0.04468792  0.99369980  1.16887 < 2.22e-16 ***
#> group0:main_sss1  0.11910569     0.04289633  0.03503042  0.20318  0.0054932 ** 
#> group1:main_sss1  1.65334374     0.04260769  1.56983421  1.73685 < 2.22e-16 ***
#> group0:main_rrr2  0.03439392     0.04311048 -0.05010107  0.11889  0.4249814    
#> group1:main_rrr2  0.03916113     0.04350030 -0.04609789  0.12442  0.3679874    
#> group0:main_sss2  0.36981341     0.04381152  0.28394440  0.45568 < 2.22e-16 ***
#> group1:main_sss2 -0.97295569     0.04406664 -1.05932472 -0.88659 < 2.22e-16 ***
#> group0:main_rrr3  0.50495588     0.04353422  0.41963037  0.59028 < 2.22e-16 ***
#> group1:main_rrr3  0.20937019     0.04406129  0.12301164  0.29573 2.0162e-06 ***
#> group0:main_sss3  0.11200873     0.04318471  0.02736826  0.19665  0.0094946 ** 
#> group1:main_sss3 -0.34948866     0.04362987 -0.43500164 -0.26398 1.1442e-15 ***
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> Transformed coefficients:
#>                   estimate standard.error    lower.CI upper.CI    p.value    
#> (Intercept)     6.95936537     0.03071384  6.89916735  7.01956 < 2.22e-16 ***
#> [group=1]      -2.54021516     0.04358864 -2.62564733 -2.45478 < 2.22e-16 ***
#> [group=0]:amp1  0.12803989     0.04327905  0.04321452  0.21287 0.00309167 ** 
#> [group=1]:amp1  1.97553184     0.04320208  1.89085732  2.06021 < 2.22e-16 ***
#> [group=0]:amp2  0.37140934     0.04374249  0.28567564  0.45714 < 2.22e-16 ***
#> [group=1]:amp2  0.97374348     0.04409110  0.88732652  1.06016 < 2.22e-16 ***
#> [group=0]:amp3  0.51722954     0.04359487  0.43178517  0.60267 < 2.22e-16 ***
#> [group=1]:amp3  0.40740422     0.04352925  0.32208845  0.49272 < 2.22e-16 ***
#> [group=0]:acr1  1.19502073     0.34087591  0.52691623  1.86313 0.00045535 ***
#> [group=1]:acr1  0.99161442     0.02232996  0.94784850  1.03538 < 2.22e-16 ***
#> [group=0]:acr2  1.47805965     0.11626128  1.25019173  1.70593 < 2.22e-16 ***
#> [group=1]:acr2 -1.53056839     0.04464781 -1.61807648 -1.44306 < 2.22e-16 ***
#> [group=0]:acr3  0.21828452     0.08337399  0.05487451  0.38169 0.00884113 ** 
#> [group=1]:acr3 -1.03105667     0.10839529 -1.24350754 -0.81861 < 2.22e-16 ***
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> ***********************
#> 
#>  Dispersion Model 
#> Raw model coefficients:
#>                       estimate standard.error      lower.CI upper.CI p.value  
#> (Intercept)      -0.0347282789   0.0224035262 -0.0786383834  0.00918 0.12111  
#> group1            0.0080079839   0.0316841767 -0.0540918612  0.07011 0.80047  
#> group0:disp_rrr1 -0.0651090414   0.0321127391 -0.1280488535 -0.00217 0.04261 *
#> group1:disp_rrr1  0.0181774870   0.0321136348 -0.0447640807  0.08112 0.57137  
#> group0:disp_sss1  0.0417504585   0.0315078818 -0.0200038551  0.10350 0.18514  
#> group1:disp_sss1  0.0036279901   0.0313982054 -0.0579113618  0.06517 0.90801  
#> group0:disp_rrr2  0.0004254641   0.0314578395 -0.0612307683  0.06208 0.98921  
#> group1:disp_rrr2  0.0240608545   0.0313904819 -0.0374633596  0.08559 0.44338  
#> group0:disp_sss2  0.0320980158   0.0322015681 -0.0310158979  0.09521 0.31887  
#> group1:disp_sss2  0.0132078872   0.0322128832 -0.0499282037  0.07634 0.68179  
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> Transformed coefficients:
#>                    estimate standard.error     lower.CI upper.CI   p.value    
#> (Intercept)    -0.034728279    0.022403526 -0.078638383  0.00918  0.121111    
#> [group=1]       0.008007984    0.031684177 -0.054091861  0.07011  0.800466    
#> [group=0]:amp1  0.077345252    0.032142190  0.014347717  0.14034  0.016113 *  
#> [group=1]:amp1  0.018536001    0.031999932 -0.044182714  0.08125  0.562420    
#> [group=0]:amp2  0.032100835    0.032186743 -0.030984022  0.09519  0.318604    
#> [group=1]:amp2  0.027447641    0.031351232 -0.033999645  0.08889  0.381308    
#> [group=0]:acr1  2.571400963    0.406978273  1.773738205  3.36906 2.645e-10 ***
#> [group=1]:acr1  0.196998411    1.700155198 -3.135244545  3.52924  0.907755    
#> [group=0]:acr2  1.557541951    0.980442018 -0.364089094  3.47917  0.112148    
#> [group=1]:acr2  0.502026527    1.175003859 -1.800938719  2.80499  0.669193    
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> ***********************
#> 
#>  Zero-Inflation Model 
#> Raw model coefficients:
#>                     estimate standard.error      lower.CI  upper.CI p.value
#> (Intercept)    -2.230760e+01   2.210633e+03 -4.355068e+03  4310.453 0.99195
#> group1         -1.971913e+00   6.325990e+03 -1.240068e+04 12396.741 0.99975
#> group0:zi_rrr1 -2.438833e-02   3.230957e+03 -6.332583e+03  6332.535 0.99999
#> group1:zi_rrr1 -1.371834e-02   8.652923e+03 -1.695943e+04 16959.405 1.00000
#> group0:zi_sss1  1.177424e-02   3.193473e+03 -6.259080e+03  6259.104 1.00000
#> group1:zi_sss1  7.523574e-03   8.560824e+03 -1.677890e+04 16778.915 1.00000
#> group0:zi_rrr2  6.932705e-03   3.221557e+03 -6.314129e+03  6314.143 1.00000
#> group1:zi_rrr2  2.619906e-03   8.626642e+03 -1.690791e+04 16907.910 1.00000
#> group0:zi_sss2  6.000491e-02   3.201407e+03 -6.274583e+03  6274.703 0.99999
#> group1:zi_sss2  3.145151e-02   8.584676e+03 -1.682562e+04 16825.687 1.00000
#> group0:zi_rrr3  2.874116e-03   3.098328e+03 -6.072609e+03  6072.615 1.00000
#> group1:zi_rrr3  1.750133e-03   8.300080e+03 -1.626786e+04 16267.860 1.00000
#> group0:zi_sss3  2.894994e-02   3.170419e+03 -6.213879e+03  6213.937 0.99999
#> group1:zi_sss3  1.434228e-02   8.498381e+03 -1.665651e+04 16656.535 1.00000
#> 
#> Transformed coefficients:
#>                     estimate standard.error      lower.CI    upper.CI p.value
#> (Intercept)    -2.230760e+01   2.210633e+03 -4.355068e+03    4310.453 0.99195
#> [group=1]      -1.971913e+00   6.325990e+03 -1.240068e+04   12396.741 0.99975
#> [group=0]:amp1  2.708179e-02   3.289847e+03 -6.447954e+03    6448.008 0.99999
#> [group=1]:amp1  1.564599e-02   8.823637e+03 -1.729399e+04   17294.026 1.00000
#> [group=0]:amp2  6.040407e-02   3.201214e+03 -6.274203e+03    6274.324 0.99998
#> [group=1]:amp2  3.156044e-02   8.583916e+03 -1.682413e+04   16824.197 1.00000
#> [group=0]:amp3  2.909226e-02   3.176143e+03 -6.225096e+03    6225.154 0.99999
#> [group=1]:amp3  1.444867e-02   8.517479e+03 -1.669394e+04   16693.967 1.00000
#> [group=0]:acr1  2.691814e+00   1.156782e+05 -2.267223e+05  226727.707 0.99998
#> [group=1]:acr1  2.639954e+00   5.359048e+05 -1.050351e+06 1050356.720 1.00000
#> [group=0]:acr2  1.455771e+00   5.333663e+04 -1.045364e+05  104539.328 0.99998
#> [group=1]:acr2  1.487688e+00   2.733611e+05 -5.357765e+05  535779.475 1.00000
#> [group=0]:acr3  1.471842e+00   1.062984e+05 -2.083396e+05  208342.533 0.99999
#> [group=1]:acr3  1.449371e+00   5.730964e+05 -1.123247e+06 1123249.747 1.00000
```

*Note that this dataset was not simulated with consideration of
dispersion or zero-inflation characteristics, hence the lack of
significant P-values in the model summary for the dispersion and
zero-inflation models.*
