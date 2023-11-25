
# plot_comparisons

Plot Conditional or Marginal Comparisons

## Description

Plot comparisons on the y-axis against values of one or more predictors
(x-axis, colors/shapes, and facets).

The <code>by</code> argument is used to plot marginal comparisons, that
is, comparisons made on the original data, but averaged by subgroups.
This is analogous to using the <code>by</code> argument in the
<code>comparisons()</code> function.

The <code>condition</code> argument is used to plot conditional
comparisons, that is, comparisons made on a user-specified grid. This is
analogous to using the <code>newdata</code> argument and
<code>datagrid()</code> function in a <code>comparisons()</code> call.

All unspecified variables are held at their mean or mode. This includes
grouping variables in mixed-effects models, so analysts who fit such
models may want to specify the groups of interest using the
<code>variables</code> argument, or supply model-specific arguments to
compute population-level estimates. See details below. See the "Plots"
vignette and website for tutorials and information on how to customize
plots:

<ul>
<li>

https://marginaleffects.com/articles/plot.html

</li>
<li>

https://marginaleffects.com

</li>
</ul>

## Usage

<pre><code class='language-R'>plot_comparisons(
  model,
  variables = NULL,
  condition = NULL,
  by = NULL,
  newdata = NULL,
  type = "response",
  vcov = NULL,
  conf_level = 0.95,
  wts = NULL,
  comparison = "difference",
  transform = NULL,
  rug = FALSE,
  gray = FALSE,
  draw = TRUE,
  ...
)
</code></pre>

## Arguments

<table>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_model">model</code>
</td>
<td>
Model object
</td>
</tr>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_variables">variables</code>
</td>
<td>
Name of the variable whose contrast we want to plot on the y-axis.
</td>
</tr>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_condition">condition</code>
</td>
<td>

Conditional slopes

<ul>
<li>

Character vector (max length 4): Names of the predictors to display.

</li>
<li>

Named list (max length 4): List names correspond to predictors. List
elements can be:

<ul>
<li>

Numeric vector

</li>
<li>

Function which returns a numeric vector or a set of unique categorical
values

</li>
<li>

Shortcut strings for common reference values: "minmax", "quartile",
"threenum"

</li>
</ul>
</li>
<li>

1: x-axis. 2: color/shape. 3: facet (wrap if no fourth variable,
otherwise cols of grid). 4: facet (rows of grid).

</li>
<li>

Numeric variables in positions 2 and 3 are summarized by Tukey’s five
numbers <code>?stats::fivenum</code>.

</li>
</ul>
</td>
</tr>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_by">by</code>
</td>
<td>

Aggregate unit-level estimates (aka, marginalize, average over). Valid
inputs:

<ul>
<li>

<code>FALSE</code>: return the original unit-level estimates.

</li>
<li>

<code>TRUE</code>: aggregate estimates for each term.

</li>
<li>

Character vector of column names in <code>newdata</code> or in the data
frame produced by calling the function without the <code>by</code>
argument.

</li>
<li>

Data frame with a <code>by</code> column of group labels, and merging
columns shared by <code>newdata</code> or the data frame produced by
calling the same function without the <code>by</code> argument.

</li>
<li>

See examples below.

</li>
<li>

For more complex aggregations, you can use the <code>FUN</code> argument
of the <code>hypotheses()</code> function. See that function’s
documentation and the Hypothesis Test vignettes on the
<code>marginaleffects</code> website.

</li>
</ul>
</td>
</tr>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_newdata">newdata</code>
</td>
<td>
When <code>newdata</code> is <code>NULL</code>, the grid is determined
by the <code>condition</code> argument. When <code>newdata</code> is not
<code>NULL</code>, the argument behaves in the same way as in the
<code>comparisons()</code> function.
</td>
</tr>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_type">type</code>
</td>
<td>
string indicates the type (scale) of the predictions used to compute
contrasts or slopes. This can differ based on the model type, but will
typically be a string such as: "response", "link", "probs", or "zero".
When an unsupported string is entered, the model-specific list of
acceptable values is returned in an error message. When
<code>type</code> is <code>NULL</code>, the first entry in the error
message is used by default.
</td>
</tr>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_vcov">vcov</code>
</td>
<td>

Type of uncertainty estimates to report (e.g., for robust standard
errors). Acceptable values:

<ul>
<li>

FALSE: Do not compute standard errors. This can speed up computation
considerably.

</li>
<li>

TRUE: Unit-level standard errors using the default
<code>vcov(model)</code> variance-covariance matrix.

</li>
<li>

String which indicates the kind of uncertainty estimates to return.

<ul>
<li>

Heteroskedasticity-consistent: <code>“HC”</code>, <code>“HC0”</code>,
<code>“HC1”</code>, <code>“HC2”</code>, <code>“HC3”</code>,
<code>“HC4”</code>, <code>“HC4m”</code>, <code>“HC5”</code>. See
<code>?sandwich::vcovHC</code>

</li>
<li>

Heteroskedasticity and autocorrelation consistent: <code>“HAC”</code>

</li>
<li>

Mixed-Models degrees of freedom: "satterthwaite", "kenward-roger"

</li>
<li>

Other: <code>“NeweyWest”</code>, <code>“KernHAC”</code>,
<code>“OPG”</code>. See the <code>sandwich</code> package documentation.

</li>
</ul>
</li>
<li>

One-sided formula which indicates the name of cluster variables (e.g.,
<code>~unit_id</code>). This formula is passed to the
<code>cluster</code> argument of the <code>sandwich::vcovCL</code>
function.

</li>
<li>

Square covariance matrix

</li>
<li>

Function which returns a covariance matrix (e.g.,
<code>stats::vcov(model)</code>)

</li>
</ul>
</td>
</tr>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_conf_level">conf_level</code>
</td>
<td>
numeric value between 0 and 1. Confidence level to use to build a
confidence interval.
</td>
</tr>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_wts">wts</code>
</td>
<td>

string or numeric: weights to use when computing average contrasts or
slopes. These weights only affect the averaging in
<code style="white-space: pre;">⁠avg\_\*()⁠</code> or with the
<code>by</code> argument, and not the unit-level estimates themselves.
Internally, estimates and weights are passed to the
<code>weighted.mean()</code> function.

<ul>
<li>

string: column name of the weights variable in <code>newdata</code>.
When supplying a column name to <code>wts</code>, it is recommended to
supply the original data (including the weights variable) explicitly to
<code>newdata</code>.

</li>
<li>

numeric: vector of length equal to the number of rows in the original
data or in <code>newdata</code> (if supplied).

</li>
</ul>
</td>
</tr>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_comparison">comparison</code>
</td>
<td>

How should pairs of predictions be compared? Difference, ratio, odds
ratio, or user-defined functions.

<ul>
<li>

string: shortcuts to common contrast functions.

<ul>
<li>

Supported shortcuts strings: difference, differenceavg,
differenceavgwts, dydx, eyex, eydx, dyex, dydxavg, eyexavg, eydxavg,
dyexavg, dydxavgwts, eyexavgwts, eydxavgwts, dyexavgwts, ratio,
ratioavg, ratioavgwts, lnratio, lnratioavg, lnratioavgwts, lnor,
lnoravg, lnoravgwts, lift, liftavg, expdydx, expdydxavg, expdydxavgwts

</li>
<li>

See the Comparisons section below for definitions of each
transformation.

</li>
</ul>
</li>
<li>

function: accept two equal-length numeric vectors of adjusted
predictions (<code>hi</code> and <code>lo</code>) and returns a vector
of contrasts of the same length, or a unique numeric value.

<ul>
<li>

See the Transformations section below for examples of valid functions.

</li>
</ul>
</li>
</ul>
</td>
</tr>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_transform">transform</code>
</td>
<td>
string or function. Transformation applied to unit-level estimates and
confidence intervals just before the function returns results. Functions
must accept a vector and return a vector of the same length. Support
string shortcuts: "exp", "ln"
</td>
</tr>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_rug">rug</code>
</td>
<td>
TRUE displays tick marks on the axes to mark the distribution of raw
data.
</td>
</tr>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_gray">gray</code>
</td>
<td>
FALSE grayscale or color plot
</td>
</tr>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_draw">draw</code>
</td>
<td>
<code>TRUE</code> returns a <code>ggplot2</code> plot.
<code>FALSE</code> returns a <code>data.frame</code> of the underlying
data.
</td>
</tr>
<tr>
<td style="white-space: nowrap; font-family: monospace; vertical-align: top">
<code id="plot_comparisons_:_...">…</code>
</td>
<td>
Additional arguments are passed to the <code>predict()</code> method
supplied by the modeling package.These arguments are particularly useful
for mixed-effects or bayesian models (see the online vignettes on the
<code>marginaleffects</code> website). Available arguments can vary from
model to model, depending on the range of supported arguments by each
modeling package. See the "Model-Specific Arguments" section of the
<code>?marginaleffects</code> documentation for a non-exhaustive list of
available arguments.
</td>
</tr>
</table>

## Value

A <code>ggplot2</code> object

## Model-Specific Arguments

Some model types allow model-specific arguments to modify the nature of
marginal effects, predictions, marginal means, and contrasts. Please
report other package-specific <code>predict()</code> arguments on Github
so we can add them to the table below.

https://github.com/vincentarelbundock/marginaleffects/issues

<table>
<tr>
<td style="text-align: left;">
Package
</td>
<td style="text-align: left;">
Class
</td>
<td style="text-align: left;">
Argument
</td>
<td style="text-align: left;">
Documentation
</td>
</tr>
<tr>
<td style="text-align: left;">
<code>brms</code>
</td>
<td style="text-align: left;">
<code>brmsfit</code>
</td>
<td style="text-align: left;">
<code>ndraws</code>
</td>
<td style="text-align: left;">
brms::posterior_predict
</td>
</tr>
<tr>
<td style="text-align: left;">
</td>
<td style="text-align: left;">
</td>
<td style="text-align: left;">
<code>re_formula</code>
</td>
<td style="text-align: left;">
brms::posterior_predict
</td>
</tr>
<tr>
<td style="text-align: left;">
<code>lme4</code>
</td>
<td style="text-align: left;">
<code>merMod</code>
</td>
<td style="text-align: left;">
<code>re.form</code>
</td>
<td style="text-align: left;">
lme4::predict.merMod
</td>
</tr>
<tr>
<td style="text-align: left;">
</td>
<td style="text-align: left;">
</td>
<td style="text-align: left;">
<code>allow.new.levels</code>
</td>
<td style="text-align: left;">
lme4::predict.merMod
</td>
</tr>
<tr>
<td style="text-align: left;">
<code>glmmTMB</code>
</td>
<td style="text-align: left;">
<code>glmmTMB</code>
</td>
<td style="text-align: left;">
<code>re.form</code>
</td>
<td style="text-align: left;">
glmmTMB::predict.glmmTMB
</td>
</tr>
<tr>
<td style="text-align: left;">
</td>
<td style="text-align: left;">
</td>
<td style="text-align: left;">
<code>allow.new.levels</code>
</td>
<td style="text-align: left;">
glmmTMB::predict.glmmTMB
</td>
</tr>
<tr>
<td style="text-align: left;">
</td>
<td style="text-align: left;">
</td>
<td style="text-align: left;">
<code>zitype</code>
</td>
<td style="text-align: left;">
glmmTMB::predict.glmmTMB
</td>
</tr>
<tr>
<td style="text-align: left;">
<code>mgcv</code>
</td>
<td style="text-align: left;">
<code>bam</code>
</td>
<td style="text-align: left;">
<code>exclude</code>
</td>
<td style="text-align: left;">
mgcv::predict.bam
</td>
</tr>
<tr>
<td style="text-align: left;">
<code>robustlmm</code>
</td>
<td style="text-align: left;">
<code>rlmerMod</code>
</td>
<td style="text-align: left;">
<code>re.form</code>
</td>
<td style="text-align: left;">
robustlmm::predict.rlmerMod
</td>
</tr>
<tr>
<td style="text-align: left;">
</td>
<td style="text-align: left;">
</td>
<td style="text-align: left;">
<code>allow.new.levels</code>
</td>
<td style="text-align: left;">
robustlmm::predict.rlmerMod
</td>
</tr>
<tr>
<td style="text-align: left;">
<code>MCMCglmm</code>
</td>
<td style="text-align: left;">
<code>MCMCglmm</code>
</td>
<td style="text-align: left;">
<code>ndraws</code>
</td>
<td style="text-align: left;">
</td>
</tr>
<tr>
<td style="text-align: left;">
</td>
</tr>
</table>

## Examples

``` r
library(marginaleffects)

mod <- lm(mpg ~ hp * drat * factor(am), data = mtcars)

plot_comparisons(mod, variables = "hp", condition = "drat")
```

![](plot_comparisons.markdown_strict_files/figure-markdown_strict/unnamed-chunk-1-1.png)

``` r
plot_comparisons(mod, variables = "hp", condition = c("drat", "am"))
```

![](plot_comparisons.markdown_strict_files/figure-markdown_strict/unnamed-chunk-1-2.png)

``` r
plot_comparisons(mod, variables = "hp", condition = list("am", "drat" = 3:5))
```

![](plot_comparisons.markdown_strict_files/figure-markdown_strict/unnamed-chunk-1-3.png)

``` r
plot_comparisons(mod, variables = "am", condition = list("hp", "drat" = range))
```

![](plot_comparisons.markdown_strict_files/figure-markdown_strict/unnamed-chunk-1-4.png)

``` r
plot_comparisons(mod, variables = "am", condition = list("hp", "drat" = "threenum"))
```

![](plot_comparisons.markdown_strict_files/figure-markdown_strict/unnamed-chunk-1-5.png)