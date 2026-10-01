# Week 01 — The butterfly as a counting process

**Phase A — Classical**

> Is sunspot emergence consistent with a memoryless (Poisson) process, and at what timescales?

## Concepts

- Recap of ButterflAI 1.0 as **given, runnable code** using `legacy/` (classical model only)
- A point process: its realization is a *set of event times*, not a vector of values
- Deriving the emergence table: one event per group, at its first observation
- Counts in a window, and the **Fano factor** F(Δ) = Var / Mean — measured as a curve across window widths
- Why rate variation also inflates F(Δ), and how to read the curve anyway
- Metrics carried over from 1.0: negative log-likelihood (NLL) and Earth Mover's Distance (EMD) on latitude

## Deliverable

F(Δ) for each hemisphere, with an interpretation: where is it near 1, where does it rise, and which explanation fits each regime?

## Notes

- The recap contains no tasks; tasks start after it. Returning participants can skim it.
- F(Δ) = 1 is *consistent with* Poisson; it does not prove it.

## Notebook

_Released at the start of the week._ Task numbering continues from the previous week.
