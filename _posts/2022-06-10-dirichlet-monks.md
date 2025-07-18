---
title: "Community Detection with Dirichlet Process Prior"
author: "Davis Berlind"
date: "2022-06-10"
permalink: "/posts/2022/06/dirichlet-monks/"
excerpt: "Implementing Bayesian nonparametric community detection for Sampson's monks."
tags:
  - Bayeseian nonparametrics
  - community dection
  - networks
  - Dirichlet process
---

## Background

In his 1968 PhD thesis at Cornell University, Samuel F. Sampson
conducted an ethnographic study of the community structure in a New
England monastery. The monks in the study were asked to rank which of
their peers they liked most and which they liked least, listing a first,
second, and third choice. This survey was performed on five separate
occasions over a 12-month period. Shortly after the fourth survey was
administered, four members of the monastery were expelled. Sampson was
interested in identifying the social dynamics that led to the
expulsions. He used his survey to classify the novice monks into three
categories: Young Turks, Loyal Opposition, Outcasts. In the [data
set](https://networkdata.ics.uci.edu/netdata/html/sampson.html), any
monk that ranked a colleague in their top three positive relations in
any survey was considered to ‘like’ that monk, resulting in an adjacency
matrix **Y** ∈ {0, 1}<sup>18 × 18</sup> with *Y*<sub>*i**j*</sub> = 1
indicating a one-way ‘liking’ relation from monk *i* to monk *j*.

Handcock, Raftery, and Tantrum (2007) developed a latent-position
clustering model to detect communities within the network of monks.
Rather than treat the number of communities as a random variable,
Handcock, Raftery, and Tantrum (2007) selected the number of communities
based on the BIC. In this post I will lay out a fully Bayesian
nonparametric approach to community detection by placing a Dirichlet
process prior on the number of communities.

## Model

Given a graph 𝒢, we assume that if nodes *i* and *j* are in the same
community, they are more likely to be connected by an edge in 𝒢. Hoff,
Raftery, and Handcock (2002) generalized this idea to a latent space
model for network data. They assume each node has a position
*Z*<sub>*i*</sub> ∈ ℝ<sup>*d*</sup> in a *d*-dimensional latent social
space. The advantage of using latent positions is that it induces
transitivity, so that if node *i* is close to node *j* in the latent
space, and node *j* is close to node *k*, then we will also have that
node *i* and *k* are close therefore more likely to be connected in 𝒢.
Formally, if we have an adjacency matrix **Y**, where
*Y*<sub>*i**j*</sub> = 1 if there is (directed) edge going from node *i*
to *j*, and *Y*<sub>*i**j*</sub> = 0 otherwise, then we have  
Handcock, Raftery, and Tantrum (2007) showed that it is possible to
include edge specific covariates, *X*<sub>*i**j*</sub>. For simplicity,
I just choose to include an intercept *β*, and focus on modeling the
latent positions, **Z**.

### Dirchlet Process Prior

To model the latent social space, Handcock, Raftery, and Tantrum (2007)
fixed some *K* ∈ ℕ and adopted a Gaussian mixture model, Handcock,
Raftery, and Tantrum (2007) fix the number of clusters *a priori* and
find the *K* that maximizes integrated likelihood (approximated by the
BIC). We can circumvent this model selection problem with a Bayesian
nonparametric approach. Note that a *K* cluster mixture model is just a
*K* + 1 mixture model with the restriction that
*λ*<sub>*k* + 1</sub> = 0. In this sense, every size *K*′ &lt; *K*
mixture model is embedded in the *K* cluster mixture model. If we let
*K* → ∞ and can find a suitable prior for
{*λ*<sub>*k*</sub>}<sub>*k* ≥ 1</sub>, then our model will account for
every possible configuration of *K*. Then, by treating the number of
mixture components as a random variable, we can correctly make
probabilistic statements about the number of clusters, including finding
the *a posteriori* most likely number of clusters.

To implement an infinite mixture model, we can adopt a Dirichlet process
(DP) prior for the distribution that generates the latent positions
*Z*<sub>*i*</sub>. The Dirichlet process is a distribution over
distributions that was first formalized by Ferguson (1973). Antoniak
(1974) showed the DP can be used as prior over distributions in mixture
models. Formally, let (*Θ*,ℱ,*G*<sub>0</sub>) be some measurable space
and let {*A*<sub>*i*</sub>}<sub>*i* = 1</sub><sup>*r*</sup> be a finite
measurable partition of *Θ*. Then for another measure on this space *G*,
we say that *G* ∼ DP(*α*,*G*<sub>0</sub>) if for some *α* &gt; 0 we have
{*G*(*A*<sub>*i*</sub>)}<sub>*i* = 1</sub><sup>*r*</sup> ∼ Dirichlet({*G*<sub>0</sub>(*A*<sub>*i*</sub>)}<sub>*i* = 1</sub><sup>*r*</sup>).
Note that for some measurable *A* ⊂ ℱ we have and thus So
*G*<sub>0</sub>, can be thought of as the mean distribution and *α*
controls the strength of concentration around this mean with
$G(A) \overset{L\_2}{\longrightarrow} G\_0(A)$ for any measurable set
*A* as *α* → ∞. Rewriting the model Handcock, Raftery, and Tantrum
(2007) to include a DP prior, we have We can adopt the same prior
assumptions as Handcock, Raftery, and Tantrum (2007) by letting *a* = 1,
*b* = 0.103/2, and *ω*<sup>2</sup> = 2.

## Acknowledgment

This post was adapted from a project for STATS 202C at UCLA that I
worked on with my colleagues Sophie Phillips and John Baierl. Thanks to
Sophie and John for their help!

## References

Antoniak, Charles E. 1974. “Mixtures of Dirichlet Processes with
Applications to Bayesian Nonparametric Problems.” *The Annals of
Statistics* 2 (6): 1152–74.

Ferguson, Thomas S. 1973. “<span class="nocase">A Bayesian Analysis of
Some Nonparametric Problems</span>.” *The Annals of Statistics* 1 (2):
209–30. <https://doi.org/10.1214/aos/1176342360>.

Handcock, Mark S., Adrian E. Raftery, and Jeremy M. Tantrum. 2007.
“Model‐based Clustering for Social Networks.” *Journal of the Royal
Statistical Society Series A* 170 (2): 301–54.
<https://EconPapers.repec.org/RePEc:bla:jorssa:v:170:y:2007:i:2:p:301-354>.

Hoff, Peter D, Adrian E Raftery, and Mark S Handcock. 2002. “Latent
Space Approaches to Social Network Analysis.” *Journal of the American
Statistical Association* 97 (460): 1090–98.
