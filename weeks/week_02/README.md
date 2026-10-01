# Week 02 — Fitting the emergence rate in 1D

**Phase A — Classical**

> Can we recover the sunspot-number bell curve as the rate of a Poisson process?

## Concepts

- The point-process log-likelihood on event times, and why the integral term is not optional
- Candidate shapes for the rate λ(s) on the wing-aligned coordinate `s`
- **Thinning** simulation: generate data from a known rate, fit it, recover the parameters, then break the fitter on purpose
- **Time-rescaling**: transform event times with the fitted cumulative rate; a correct model gives Exp(1) gaps
- QQ plots as the goodness-of-fit test

## Deliverable

A 1D rate fit for each hemispheric cycle, validated on simulated data and checked with a time-rescaling QQ plot.

## Notes

- Fits are per hemispheric cycle and independent this week. Pooling comes later.

## Notebook

_Released at the start of the week._ Task numbering continues from the previous week.
