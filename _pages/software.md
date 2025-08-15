---
layout: archive
title: "Software"
permalink: /software/
author_profile: true
---

## R Packages

* [{mich}](/mich/) -- An R package that implements the Multiple Independent Change-Point (MICH) method introduced in [Berlind, Cappello, Madrid Padilla (2025)](https://arxiv.org/abs/2507.01558). The package's main function `mich()` implements a backfitting procedure to identify changes in the mean and/or variance of a length \\(T\\) sequence of observations \\(\mathbf{y}_{1:T}\\). The `mich.fit` object returned by `mich()` provides a variational approximation to the posterior distribution of the change-points.