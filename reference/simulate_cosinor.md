# Simulate data from a cosinor model

This function simulates data from a cosinor model with a single
covariate, where the time scale is month, and optionally allows for
single covariate effects on the mean, amplitude, and acrophase.

## Usage

``` r
simulate_cosinor(
  n,
  mesor,
  amp,
  acro,
  period = 24,
  n_components,
  beta.group = FALSE,
  beta.mesor,
  beta.amp,
  beta.acro,
  n_period = 1,
  family = c("gaussian", "poisson", "binomial", "gamma"),
  ...
)
```

## Arguments

- n:

  The sample size. An `integer` greater than 0.

- mesor:

  A `numeric`. The MESOR (midline estimating statistic of rhythm) for
  `group = 0`. The MESOR is independent of the cosinor components, so
  only one value is allowed even if there are multiple components in the
  data being simulated.

- amp:

  A `numeric`. The amplitude value (for `group = 0` if grouped data are
  being simulated (`beta.group = TRUE`)). If simulating data with
  multiple components, specify a vector with values for each component.
  E.g: `amp = c(5, 10)`.

- acro:

  A `numeric`. The acrophase value in radians (for `group = 0` if
  grouped data are being simulated (`beta.group = TRUE`)). If simulating
  data with multiple components, specify a vector with values for each
  component. E.g: `acr = c(0, pi)` for two components.

- period:

  The period of the rhythm data (for `group = 0` if grouped data are
  being simulated (`beta.group = TRUE`)). If simulating data with
  multiple components, specify a vector with values for each component.
  E.g: `period = c(12, 6)` for two components.

- n_components:

  The number of components in the model. This must match the length of
  the inputs for `amp` and `acro`.

- beta.group:

  A `logical`. If `TRUE` a second group of data will be simulated and
  included in the returned data set. If `FALSE`, `beta.acro`,
  `beta.mesor`, and `beta.amp` arguments will be ignored.

- beta.mesor:

  A `numeric`. The MESOR value term for `group = 1`

- beta.amp:

  A `numeric`. The amplitude value for `group = 1`. If simulating data
  with multiple components, specify a vector with values for each
  component. E.g: `amp = c(2, 8)`.

- beta.acro:

  A `numeric`. The acrophase value in radians (for `group = 1`. If
  simulating data with multiple components, specify a vector with values
  for each component. E.g: `acr = c(2, 5)` for two components.

- n_period:

  A `numeric`. The number of cycles of the rhythm to be simulated.

- family:

  A `character`. The family (see
  [`?family`](https://rdrr.io/r/stats/family.html)) of the simulated
  dataset. Can handle values in
  `c("poisson", "binomial", "gamma", "gaussian")`.

- ...:

  Extra arguments, including `alpha` that controls the `shape` argument
  when sampling from a gamma distribution (when `family = "gamma"`;
  default is 1), and `sd` (standard deviation) which is used when
  sampling from a normal distribution (when `family = "gaussian"`;
  default is 1). To specify these parameters for the beta (treatment)
  group, use `beta.alpha` and `beta.sd`

## Value

Returns simulated data in a `data.frame`.

## Examples

``` r
simulate_cosinor(
  n = 100,
  mesor = 1,
  amp = 1,
  acro = 1,
  period = 24,
  family = "gaussian"
)
#>               Y      times group
#> 1   -0.34131059  7.4634548     0
#> 2    0.20178990 12.8886478     0
#> 3    1.15499651 11.1739296     0
#> 4    0.53604345  5.3081749     0
#> 5    1.34081660  8.9460245     0
#> 6    1.82068220  8.4286489     0
#> 7    1.36611289 16.9868019     0
#> 8   -0.55721085  9.8145850     0
#> 9   -0.75197700 18.6612947     0
#> 10   1.21634604  8.3668912     0
#> 11   1.40511485 22.6856080     0
#> 12   1.87614889 14.0838559     0
#> 13   0.45735274 19.5362527     0
#> 14   1.02953350  0.9787463     0
#> 15  -1.24517226 17.6257065     0
#> 16   1.39462593  6.7100163     0
#> 17   3.68366739  3.4565204     0
#> 18   0.94466723 14.0268928     0
#> 19   2.31452099  7.4523821     0
#> 20   0.22900381 14.8343206     0
#> 21   0.04727344 11.8974671     0
#> 22   1.20829437 18.5688837     0
#> 23   1.57266883 20.8856291     0
#> 24   0.38899177 20.4608388     0
#> 25   1.18358590 12.6873480     0
#> 26   1.48526179  8.0136858     0
#> 27  -0.36495668 14.5856712     0
#> 28   1.59109750  3.6204695     0
#> 29  -1.49269689 14.3995877     0
#> 30   1.32111250 21.2452399     0
#> 31   2.01649362  1.4361358     0
#> 32   1.36280701 23.3646671     0
#> 33   0.51106378 16.4551474     0
#> 34   0.50866877  5.7759504     0
#> 35   2.20938140  4.7712581     0
#> 36   1.65169977  7.7139340     0
#> 37  -0.45208664 20.7051701     0
#> 38   1.04835764  0.2832798     0
#> 39  -0.45663050 15.0817796     0
#> 40   2.26646543  4.3882411     0
#> 41   0.48508441 13.5442148     0
#> 42   1.37513729 17.4825389     0
#> 43  -0.36077934 11.6615412     0
#> 44   2.53455769  4.8826947     0
#> 45   2.73244342  9.5329781     0
#> 46   2.20478851 20.5389053     0
#> 47  -0.62357732 18.6714399     0
#> 48   1.52728527 22.5791334     0
#> 49   0.87782411 18.0893958     0
#> 50   3.45066789  2.7864827     0
#> 51  -0.05850065 23.3388290     0
#> 52   0.58371641 23.0470447     0
#> 53   1.96667108 10.5019208     0
#> 54   0.18166670 18.8192481     0
#> 55   2.14877464  2.8523049     0
#> 56   0.06895145 22.8810225     0
#> 57   0.26851152  1.3653004     0
#> 58  -0.67920455 19.2020651     0
#> 59   0.47267095  5.0949867     0
#> 60   2.82585371  0.6331358     0
#> 61   2.46905265  6.4213729     0
#> 62   1.90640305 10.6031626     0
#> 63   0.48100949 12.2875878     0
#> 64   0.35912953 22.4900520     0
#> 65  -0.39590755 11.5764539     0
#> 66   1.26976882 20.9175497     0
#> 67   1.11057796 21.9282940     0
#> 68   3.40474788  8.2421640     0
#> 69   1.45889345 10.7901650     0
#> 70  -0.26481263  4.6322956     0
#> 71   2.07248306 19.9102214     0
#> 72   2.38086693  6.9448170     0
#> 73   0.59226505  3.6679975     0
#> 74   2.48903451  1.9905571     0
#> 75   1.53912438 18.2347859     0
#> 76   0.72398487 18.6702839     0
#> 77   3.20396616  3.7402453     0
#> 78   0.05642913 19.6956674     0
#> 79   1.13768949  9.3087544     0
#> 80   1.88686774 10.1575732     0
#> 81   2.80113084  1.7622496     0
#> 82   1.26850768 16.4974746     0
#> 83   1.86767998 19.1097546     0
#> 84   0.73409941  6.7950034     0
#> 85   0.83800076 20.9746699     0
#> 86   1.31852146  5.6267204     0
#> 87   0.96214002 12.6759496     0
#> 88   2.13800188 10.2866467     0
#> 89   1.27243915 17.9728965     0
#> 90   0.76590678 22.3006058     0
#> 91  -0.01110890 10.7532949     0
#> 92   0.52591004 12.6714357     0
#> 93   0.80104929  1.6687625     0
#> 94   0.15526973 10.9366812     0
#> 95  -0.11916415 21.5399937     0
#> 96   0.89877427  8.0772658     0
#> 97   0.44153431 15.5379729     0
#> 98   1.10148880  4.6563908     0
#> 99   1.28632250  6.8793524     0
#> 100  0.73146790 10.6739525     0
```
