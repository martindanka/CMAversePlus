
<!-- README.md is generated from README.Rmd. Please edit that file -->

# CMAversePlus: a friendly fork of CMAverse with extra g-formula options

<img src="man/figures/logo2.png" align="right" height="300" alt="CMAversePlus logo"/>

**CMAversePlus** is a small unofficial add-on to the excellent
[CMAverse](https://github.com/BS1125/CMAverse) package. I created this
fork (see below) for teaching, personal use, and a few specific
projects. I have tried to keep this page accessible to those who are new
to R and/or GitHub.

- **CMAverse** is a widely used R package for causal mediation analysis.
- **CMAversePlus** keeps the same workflow and commands, but adds a few
  **extra options for g-computation**, specifically risk ratios / risk
  differences for binary outcomes, a more standard version of
  interventional effects, and minor labelling changes.

*A fork* is simply a copy of another package’s code that someone adapts
for their own needs. This fork is **not officially part of CMAverse**
and **I am not afiliated with the original package**. If you want the
stable, fully supported version, use CMAverse itself. If you need these
specific extras, feel free to use CMAversePlus.

#### Why are these functions not part of CMAverse?

I created this add-on to support my own teaching and projects. It has
been tested for the purposes described here, but I have not aimed for
full integration with every feature of CMAverse. Achieving that would
require additional work and discussion with the original authors. I
haven’t contacted them yet, although I may do so in future.

## What’s different? (when `model = "gformula"`)

| Feature | What it does | Use in | Argument | Options | Default | Recommended |
|:---|:---|:---|:--:|:--:|:--:|:--:|
| Effect measures for binary outcomes | Choose odds ratio (OR), risk ratio (RR), or risk difference (RD) | `cmest(model = 'gformula')`<br>Binary outcomes only | `binary_scale` | `'OR' / 'RR' / 'RD'` | `'OR'` | `'RR'` or `'RD'` |
| Conditional mediator draws | Choose conditional vs marginal mediator draws for interventional effects | `cmest(model = 'gformula')`<br>Interventional effects | `draw_conditional` | `TRUE / FALSE` | `FALSE` | `TRUE` |
| Total/overall effect label | Switch labels automatically between the total effect (te/Rte) and its randomised analogue (rte/rRte) | `cmest(model = 'gformula')`<br>Printing/summary only | — | — | — | — |

*By default, both toggles are off. If you don’t change them,
CMAversePlus behaves exactly like CMAverse.*

## New features (details)

> **Note (scope)**\
> All new options currently target **single-mediator** settings. If you
> use multiple mediators, use the original package or proceed at your
> own risk.

**1) Additional effect measures for binary outcomes.**\
Use `binary_scale` to choose which effects are reported under
g-computation:

- `"OR"` — odds ratio (CMAverse default)\
- `"RR"` — risk ratio\
- `"RD"` — risk difference

Risk ratios/differences are often more intuitive to interpret than odds
ratios.

**2) Interventional effects via conditional draws of M.**\
Set `draw_conditional = TRUE` to compute interventional effects using a
conditional mediator draw. The CMAverse implementation uses a marginal
draw (via permutation); keeping `draw_conditional = FALSE` preserves
that behaviour.

Both approaches are valid and often yield similar results in practice.
The conditional draw is the more standard definition ([VanderWeeele et
al.,
2014](https://journals.lww.com/epidem/pages/articleviewer.aspx?year=2014&issue=03000&article=00022&type=Fulltext);
[Vansteelandt & Daniel,
2017](https://journals.lww.com/epidem/abstract/2017/03000/interventional_effects_for_mediation_analysis_with.15.aspx))
and often corresponds to more sensible hypothetical interventions. It
also relies on a slightly weaker positivity condition than the marginal
draw (discussed in [Nguyen et al.,
2020](https://psycnet.apa.org/doiLanding?doi=10.1037%2Fmet0000299)).

**3) Effect labels for the total effect.**\
When `model = "gformula"`:

- If `postc` is not specified, the total effect is shown as `te` /
  `Rte`.
- If `postc` is specified, the total effect is shown as `rte` / `rRte`.

The sum/product of the interventional direct and indirect effect is
**not** the standard total effect. Following CMAverse convention for
other effects, CMAversePlus will label this quantity as the **randomised
total effect** (`rte`) for clarity (also known as the *overall effect*).

## Installation

``` r
# install remotes if needed
if (!requireNamespace("remotes", quietly = TRUE)) {
  install.packages("remotes")
}

# install CMAversePlus
remotes::install_github("martindanka/CMAversePlus")
```

If you prefer not to use GitHub installs, you can download the package
archive (`.zip` / `.tar.gz`) from the repository and use **RStudio →
Tools → Install Packages… → Install from: Package Archive File**.

## Minimal example

Tutorials on using the original CMAverse package are provided on the
[official website](https://bs1125.github.io/CMAverse/). Below is a
minimal example where the CMAversePlus toggles are applied to a
simulated dataset from the original [CMAverse
vignette](https://bs1125.github.io/CMAverse/articles/post_exposure_confounding.html).

When you install CMAversePlus, it is recommended that you always specify
whether you want functions from CMAverse or CMAversePlus, e.g., use
`CMAversePlus::cmest()` or `CMAverse::cmest()`rather than `cmest()`.

``` r
# Load CMAversePlus
library(CMAversePlus)
```

``` r
# Simulate CMAverse example dataset
set.seed(1)
expit <- function(x) exp(x)/(1+exp(x))
n <- 10000
C1 <- rnorm(n, mean = 1, sd = 0.1)
C2 <- rbinom(n, 1, 0.6)
A <- rbinom(n, 1, expit(0.2 + 0.5*C1 + 0.1*C2))
L <- rnorm(n, mean = 1 + A - C1 - 0.5*C2, sd = 0.5)
M <- rbinom(n, 1, expit(1 + 2*A - L + 1.5*C1 + 0.8*C2))
Y <- rbinom(n, 1, expit(-3 - 0.4*A - 1.2*M + 0.5*A*M - 0.5*L + 0.3*C1 - 0.6*C2))
data <- data.frame(A, M, Y, C1, C2, L)
```

Now we can request interventional effects on the risk ratio scale. We
will also request the conditional draw from the mediator distribution
instead of the marginal default.

``` r
fit <- CMAversePlus::cmest(
  data = data, 
  model = "gformula", 
  outcome = "Y", 
  exposure = "A", 
  mediator = "M", 
  basec = c("C1", "C2"), 
  postc = "L", 
  EMint = TRUE, 
  mreg = list("logistic"), 
  yreg = "logistic", 
  postcreg = list("linear"), 
  astar = 0, 
  a = 1, 
  mval = list(1), 
  estimation = "imputation", 
  inference = "bootstrap", 
  nboot = 2,
  # CMAversePlus-only toggles:
  binary_scale = "RR",
  draw_conditional = TRUE 
  )
```

    ##   |                                                                              |                                                                      |   0%

    ## Warning in estinf(): yval is not specified; 1 is used

    ##   |                                                                              |===================================                                   |  50%  |                                                                              |======================================================================| 100%

``` r
summary(fit)
```

    ## Causal Mediation Analysis
    ## 
    ## # Outcome regression:
    ## 
    ## Call:
    ## glm(formula = Y ~ A + M + A * M + C1 + C2 + L, family = binomial(), 
    ##     data = getCall(x$reg.output$yreg)$data, weights = getCall(x$reg.output$yreg)$weights)
    ## 
    ## Coefficients:
    ##             Estimate Std. Error z value Pr(>|z|)   
    ## (Intercept)  -2.7488     0.9365  -2.935  0.00333 **
    ## A            -0.1267     0.6617  -0.192  0.84812   
    ## M            -0.7274     0.4136  -1.759  0.07863 . 
    ## C1           -0.1906     0.8705  -0.219  0.82664   
    ## C2           -0.5566     0.1933  -2.880  0.00397 **
    ## L            -0.2242     0.1709  -1.312  0.18964   
    ## A:M          -0.3425     0.6635  -0.516  0.60572   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 1447.7  on 9999  degrees of freedom
    ## Residual deviance: 1415.5  on 9993  degrees of freedom
    ## AIC: 1429.5
    ## 
    ## Number of Fisher Scoring iterations: 7
    ## 
    ## 
    ## # Mediator regressions: 
    ## 
    ## Call:
    ## glm(formula = M ~ A + C1 + C2 + L, family = binomial(), data = getCall(x$reg.output$mreg[[1L]])$data, 
    ##     weights = getCall(x$reg.output$mreg[[1L]])$weights)
    ## 
    ## Coefficients:
    ##             Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)   0.9946     0.6067   1.639   0.1012    
    ## A             1.8884     0.1726  10.944  < 2e-16 ***
    ## C1            1.4955     0.6090   2.456   0.0141 *  
    ## C2            0.8166     0.1410   5.793 6.92e-09 ***
    ## L            -0.9171     0.1215  -7.546 4.50e-14 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 2646.0  on 9999  degrees of freedom
    ## Residual deviance: 2397.3  on 9995  degrees of freedom
    ## AIC: 2407.3
    ## 
    ## Number of Fisher Scoring iterations: 7
    ## 
    ## 
    ## # Regressions for mediator-outcome confounders affected by the exposure: 
    ## 
    ## Call:
    ## glm(formula = L ~ A + C1 + C2, family = gaussian(), data = getCall(x$reg.output$postcreg[[1L]])$data, 
    ##     weights = getCall(x$reg.output$postcreg[[1L]])$weights)
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  1.00268    0.05077   19.75   <2e-16 ***
    ## A            1.00202    0.01081   92.68   <2e-16 ***
    ## C1          -1.00369    0.04980  -20.15   <2e-16 ***
    ## C2          -0.49437    0.01032  -47.92   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for gaussian family taken to be 0.2539334)
    ## 
    ##     Null deviance: 5324.2  on 9999  degrees of freedom
    ## Residual deviance: 2538.3  on 9996  degrees of freedom
    ## AIC: 14678
    ## 
    ## Number of Fisher Scoring iterations: 2
    ## 
    ## 
    ## # Effect decomposition on the risk ratio scale via the g-formula approach
    ##  
    ## Direct counterfactual imputation estimation with 
    ##  bootstrap standard errors, percentile confidence intervals and p-values 
    ##  
    ## Mediator draws: conditional
    ## 
    ##                  Estimate Std.error   95% CIL 95% CIU  P.val    
    ## Rcde             0.504946  0.132593  0.354710   0.533 <2e-16 ***
    ## rRpnde           0.527172  0.114841  0.434969   0.589 <2e-16 ***
    ## rRtnde           0.512644  0.122663  0.389883   0.555 <2e-16 ***
    ## rRpnie           0.964164  0.019986  0.947993   0.975 <2e-16 ***
    ## rRtnie           0.937592  0.013783  0.873797   0.892 <2e-16 ***
    ## rRte             0.494272  0.108478  0.380074   0.526 <2e-16 ***
    ## ERcde           -0.468424  0.140011 -0.616369  -0.428 <2e-16 ***
    ## rERintref       -0.004404  0.025170  0.018123   0.052 <2e-16 ***
    ## rERintmed        0.002936  0.013623 -0.029744  -0.011 <2e-16 ***
    ## rERpnie         -0.035836  0.019986 -0.051997  -0.025 <2e-16 ***
    ## ERcde(prop)      0.926238  0.067808  0.903554   0.995 <2e-16 ***
    ## rERintref(prop)  0.008708  0.034004 -0.083572  -0.038 <2e-16 ***
    ## rERintmed(prop) -0.005806  0.017799  0.023961   0.048 <2e-16 ***
    ## rERpnie(prop)    0.070861  0.051603  0.041044   0.110 <2e-16 ***
    ## rpm              0.065055  0.033803  0.088919   0.134 <2e-16 ***
    ## rint             0.002901  0.016205 -0.035697  -0.014 <2e-16 ***
    ## rpe              0.073762  0.067808  0.005346   0.096 <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Rcde: controlled direct effect risk ratio; rRpnde: randomized analogue of pure natural direct effect risk ratio; rRtnde: randomized analogue of total natural direct effect risk ratio; rRpnie: randomized analogue of pure natural indirect effect risk ratio; rRtnie: randomized analogue of total natural indirect effect risk ratio; Rte: total effect risk ratio; ERcde: excess relative risk due to controlled direct effect; rERintref: randomized analogue of excess relative risk due to reference interaction; rERintmed: randomized analogue of excess relative risk due to mediated interaction; rERpnie: randomized analogue of excess relative risk due to pure natural indirect effect; ERcde(prop): proportion ERcde; rERintref(prop): proportion rERintref; rERintmed(prop): proportion rERintmed; rERpnie(prop): proportion rERpnie; rpm: randomized analogue of overall proportion mediated; rint: randomized analogue of overall proportion attributable to interaction; rpe: randomized analogue of overall proportion eliminated)
    ## 
    ## Relevant variable values: 
    ## $a
    ## [1] 1
    ## 
    ## $astar
    ## [1] 0
    ## 
    ## $yval
    ## [1] "1"
    ## 
    ## $mval
    ## $mval[[1]]
    ## [1] 1

## Further CMAverse resources

See the CMAverse website: <https://bs1125.github.io/CMAverse/>

## Attribution

CMAversePlus is only a small addition that builds directly on CMAverse.
Please **cite CMAverse** when you use this fork. If you specifically
rely on the add-ons here, you’re welcome to reference this repository as
well.
