# Generates a polar plot with elliptical confidence intervals

Generates a polar plot with elliptical confidence intervals

## Usage

``` r
# S3 method for class 'cglmm'
polar_plot(
  x,
  ci_level = 0.95,
  n_breaks = 5,
  component_index = NULL,
  grid_angle_segments = 8,
  radial_units = c("radians", "degrees", "period"),
  clockwise = FALSE,
  text_size = 3.5,
  text_opacity = 1,
  fill_colors,
  ellipse_opacity = 0.3,
  circle_linetype = "dotted",
  start = c("right", "left", "top", "bottom"),
  view = c("full", "zoom", "zoom_origin"),
  overlay_parameter_info = FALSE,
  quietly = TRUE,
  show_component_labels = TRUE,
  xlims,
  ylims,
  ...
)
```

## Arguments

- x:

  An object of class `cglmm`

- ci_level:

  The level for calculated confidence ellipses. Defaults to 0.95.

- n_breaks:

  The number of concentric circles that will be plotted using the
  [`scales::breaks_pretty()`](https://scales.r-lib.org/reference/breaks_pretty.html)
  function. By default, 5 breaks will be used. The number of breaks may
  be adjusted to result in an even interval. For example, if n_breaks is
  3, but the maximum plot radius is 8, instead of plotting circles in
  intervals in 1.6, this interval will be rounded to 2 to result in the
  sequence: 0, 2, 4, 6, 8. See
  [`?scales::breaks_pretty`](https://scales.r-lib.org/reference/breaks_pretty.html)
  for more details.

- component_index:

  A number that corresponds to a particular component from the
  [`cglmm()`](https://docs.ropensci.org/GLMMcosinor/reference/cglmm.md)
  object that will be used to create polar plot. If missing (default),
  then plots for all components will be arranged in the returned plot.
  If a single or multiple values are provided, then these components
  will be returned. (for example `component_index = 1`,
  `component_index = c(1, 3)`).

- grid_angle_segments:

  An `integer`. Determines the total number of segments in the
  background of the polar plot. For example, a value of 4 will create
  quadrants around the origin. Defaults to 8.

- radial_units:

  A `character` specifying the angular units of the plot. Possible
  values are one of `c('radians', 'degrees', 'period')`. These units
  relate to the period of the component being visualized.

  `'radians'`: \\\[0, 2\pi\]\\

  :   

  `'degrees'`: \\\[0, 360\]\\

  :   

  `'period'`: \\\[0, period\]\\

  :   

- clockwise:

  A `logical`. If `TRUE`, the angles increase in a clockwise fashion. If
  `FALSE`, anti-clockwise. Defaults to `FALSE`.

- text_size:

  A number controlling the font size of the text labels. Defaults to 3.

- text_opacity:

  A `numeric` between 0 and 1 inclusive that controls the opacity of the
  text labels.

- fill_colors:

  A `character` vector containing colors that will be mapped to levels
  within a group. If the model has components with different number of
  levels per factor, the length of this input should match the greatest
  number of levels. If not, or if the number of levels exceeds the
  length of the default argument (8), colors are generated using
  [`rainbow()`](https://rdrr.io/r/grDevices/palettes.html).

- ellipse_opacity:

  A `numeric` between 0 and 1 inclusive that controls the opacity of the
  confidence ellipses. Defaults to 0.3.

- circle_linetype:

  A `character` or `numeric` that determines the `linetype` of the
  radial circles in background of the polar plot. See `?linetype` for
  more details.

- start:

  A `character`, within `c("right", "left", "top", "bottom")` that
  determines where angle 0 is located. If `start = "top"`, and
  `clockwise = TRUE`, the angle will rotate clockwise, starting at the
  '12 o-clock' position on a clock.

- view:

  A `character`, within `c("full", "zoom", "zoom_origin")` that controls
  the view of the plots.

  `'full'`: maintains a full view of the polar plot, including the background radial circles.

  :   

  `'zoom'`: finds the minimum view window which contains all confidence ellipses.

  :   

  `'zoom_origin'`: zooms into the confidence ellipses (like "zoom"), but also keeps the origin within frame.

  :   

- overlay_parameter_info:

  A `logical` argument. If `TRUE`, more information about the acrophase
  and amplitude are displayed on the polar plots.

- quietly:

  Analogous to verbose, this `logical` argument controls whether
  messages are displayed in the console.

- show_component_labels:

  Logical argument, TRUE by default. When TRUE, the polar plots have
  labels corresponding to their components.

- xlims:

  A vector of length two containing the limits for the x-axis.

- ylims:

  A vector of length two containing the limits for the y-axis.

- ...:

  Additional, ignored arguments.

## Value

Returns a `ggplot` object.

## Examples

``` r
model <- cglmm(
  vit_d ~ X + amp_acro(time, group = "X", period = 12),
  data = vitamind
)
polar_plot(model, radial_units = "period")
```
