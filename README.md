
<!-- README.md is generated from README.Rmd. Please edit that file -->

# CMAversePlus: a friendly fork of CMAverse with extra g-formula options

<img src="man/figures/logo2.png" align="right" height="300" alt="CMAversePlus logo"/>

**CMAversePlus** is a small “add-on package” based on the excellent
[CMAverse](https://github.com/BS1125/CMAverse). I created this fork (see
below) for teaching, personal use, and a few specific projects. I have
tried to keep this page accessible to those who are new to R and/or
GitHub.

- **CMAverse** is a widely used R package for causal mediation analysis.
- **CMAversePlus** keeps the same workflow and commands, but adds a few
  **extra options for g-computation**, specifically risk ratios / risk
  differences for binary outcomes, a more standard version of
  interventional effects, and minor labelling changes.

*A fork* is simply a copy of another package’s code that someone adapts
for their own needs. This fork is **not officially part of CMAverse**.
If you want the stable, fully supported version, use CMAverse itself. If
you need these specific extras, feel free to use CMAversePlus.

#### Why are these functions not part of CMAverse?

CMAversePlus was created with specific teaching and project needs in
mind. It works well for this purpose, but it has not yet been fully
integrated with every feature of CMAverse. Achieving that would require
additional work and discussion with the original authors. I haven’t
contacted them yet, although I may do so in future.

## What’s different? (when `model = "gformula"`)

| Feature                             | What it does                                                             | Use in                                                |      Argument      |       Options        | Default |   Recommended    |
|:------------------------------------|:-------------------------------------------------------------------------|:------------------------------------------------------|:------------------:|:--------------------:|:-------:|:----------------:|
| Effect measures for binary outcomes | Choose odds ratio (OR), risk ratio (RR), or risk difference (RD)         | `cmest(model = 'gformula')`<br>Binary outcomes only   |   `binary_scale`   | `'OR' / 'RR' / 'RD'` | `'OR'`  | `'RR'` or `'RD'` |
| Conditional mediator draws          | Choose conditional vs marginal mediator draws for interventional effects | `cmest(model = 'gformula')`<br>Interventional effects | `draw_conditional` |    `TRUE / FALSE`    | `FALSE` |      `TRUE`      |
| Effect labels                       | Switch labels automatically between natural and interventional effects   | `cmest(model = 'gformula')`<br>Printing/summary only  |         —          |          —           |    —    |        —         |

*By default, both toggles are off. If you don’t change them,
CMAversePlus behaves exactly like CMAverse.*

## New features (details)

> **Note (scope)**  
> All new options currently target **single-mediator** settings. If you
> use multiple mediators, use the original package or proceed at your
> own risk.

**1) Additional effect measures for binary outcomes.**  
Use `binary_scale` to choose which effects are reported under
g-computation:

- `"OR"` — odds ratio (CMAverse default)  
- `"RR"` — risk ratio  
- `"RD"` — risk difference

Risk ratios/differences are often more intuitive to interpret than odds
ratios.

**2) Interventional effects via conditional draws of M.**  
Set `draw_conditional = TRUE` to compute interventional effects using a
conditional mediator draw. The CMAverse implementation uses a marginal
draw (via permutation); keeping `draw_conditional = FALSE` preserves
that behaviour.

Both approaches are valid and often yield similar results in practice.
The conditional draw is the more standard definition and often
corresponds to more sensible hypothetical interventions. It also relies
on a slightly weaker positivity condition than the marginal draw
(discussed in [Nguyen et al.,
2020](https://psycnet.apa.org/doiLanding?doi=10.1037%2Fmet0000299)).

**3) Effect labels for interventional effects.**  
When `model = "gformula"`:

- If `postc` is specified, labels use the randomised-analogue notation
  (e.g., `rpnde`, and total effects are shown as `rte` / `rRte`).
- If `postc` is not specified, labels use natural effect notation (e.g.,
  `pnde`, `te` / `Rte`).

Importantly, when intermediate confounding is present, the sum/product
of the interventional direct and indirect effect is **not** the standard
total effect. We label that quantity as the **randomised total effect**
(`rte`) for clarity (also known as the *overall effect*).

## Installation

``` r
# install remotes if needed
if (!requireNamespace("remotes", quietly = TRUE)) {
  install.packages("remotes")
}

# install CMAversePlus (replace with your fork if different)
remotes::install_github("martindanka/CMAversePlus")
```

If you prefer not to use GitHub installs, you can download the package
archive (`.zip` / `.tar.gz`) from the repository and use **RStudio →
Tools → Install Packages… → Install from: Package Archive File**.

## Further CMAverse resources

See the CMAverse website: <https://bs1125.github.io/CMAverse/>

## Attribution

CMAversePlus is only a small addition that builds directly on CMAverse.
Please **cite CMAverse** when you use this fork. If you specifically
rely on the add-ons here, you’re welcome to reference this repository as
well.
