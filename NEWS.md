# CMAversePlus 0.1.1

## Bug fix

* `draw_conditional = TRUE` with intermediate confounders (`postc`) previously
  simulated the mediator from the same simulated values of the intermediate
  confounders used for the outcome. This preserved dependence between L and M
  in estimated same-world quantities (and, for continuous L, also affected the
  cross-world quantities), and therefore the reported effects were not the
  standard interventional analogues of natural effects, where the mediator is
  drawn from its distribution within C only. This has been fixed so that the
  mediator is now simulated using its own, independent draw of L, separate
  from the L used for the outcome.

* **Action needed:** results obtained with `draw_conditional = TRUE` and a
  non-empty `postc` under version 0.1.0 should be re-run. Results with the
  default `draw_conditional = FALSE` (marginal draws) are unchanged.

# CMAversePlus 0.1.0

* Initial release, extending CMAverse's g-formula estimator with:
  * `draw_conditional` — conditional (rather than permuted/marginal) draws
    from the mediator distribution for interventional effects.
  * `binary_scale` — effect scale for binary outcomes: `"OR"` (default),
    `"RR"`, or `"RD"`.
