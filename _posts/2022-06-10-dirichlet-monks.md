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

In his 1968 PhD thesis at Cornell University, Samuel F. Sampson conducted an ethnographic study of the community structure in a New England monastery. The monks in the study were asked to rank which of their peers they liked most and which they liked least, listing a first, second, and third choice. This survey was performed on five separate occasions over a 12-month period. Shortly after the fourth survey was administered, four members of the monastery were expelled. Sampson was interested in identifying the social dynamics that led to the expulsions. He used his survey to classify the novice monks into three categories: Young Turks, Loyal Opposition, Outcasts. In the [data set](https://networkdata.ics.uci.edu/netdata/html/sampson.html), any monk that ranked a colleague in their top three positive relations in any survey was considered to 'like' that monk, resulting in an adjacency matrix $\mathbf{Y}\in\{0,1\}^{18\times 18}$ with $Y_{ij}=1$ indicating a one-way 'liking' relation from monk $i$ to monk $j$. 

Handcock, Raftery, and Tantrum (2007) developed a latent-position clustering model to detect communities within the network of monks. Rather than treat the number of communities as a random variable, Handcock, Raftery, and Tantrum (2007) selected the number of communities based on the BIC. In this post I will lay out a fully Bayesian nonparametric approach to community detection by placing a Dirichlet process prior on the number of communities.

## Model

Given a graph $\mathcal{G}$, we assume that if nodes $i$ and $j$ are in the same community, they are more likely to be connected by an edge in $\mathcal{G}$. Hoff, Raftery, and Handcock (2002) generalized this idea to a latent space model for network data. They assume each node has a position $Z_i \in \mathbb{R}^d$ in a $d$-dimensional latent social space. The advantage of using latent positions is that it induces transitivity, so that if node $i$ is close to node $j$ in the latent space, and node $j$ is close to node $k$, then we will also have that node $i$ and $k$ are close therefore more likely to be connected in $\mathcal{G}$. Formally, if we have an adjacency matrix $\mathbf{Y}$, where $Y_{ij} = 1$ if there is (directed) edge going from node $i$ to $j$, and $Y_{ij} = 0$ otherwise, then we have  
\begin{gather*}
    p(\mathbf{Y} \;|\; \mathbf{Z}, \beta) = \prod_{i \neq j} p(Y_{ij} \;|\; Z_i, Z_j), \\
    Y_{ij} \;|\; Z_i, Z_j, \beta \overset{\text{ind.}}{\sim} \text{Bernoulli}(\pi_{ij}), \\ 
    \pi_{ij} = \text{Logistic}(\beta - \lVert Z_i - Z_j \rVert_2).
\end{gather*}  
Handcock, Raftery, and Tantrum (2007) showed that it is possible to include edge specific covariates, $X_{ij}$. For simplicity, I just choose to include an intercept $\beta$, and focus on modeling the latent positions, $\mathbf{Z}$.

### Dirchlet Process Prior

To model the latent social space, Handcock, Raftery, and Tantrum (2007)
fixed some $K \in \mathbb{N}$ and adopted a Gaussian mixture model,
\begin{align*}
    X_{ik} &\overset{\text{ind.}}{\sim} \mathcal{N}_d(\mu_k, \sigma_k^2 \mathbf{I}_d), \\
    Z_i &= \sum_{k=1}^K \lambda_k X_{ik}, \\
    \lambda &\sim \text{Dirichlet}(\nu).
\end{align*}
Handcock, Raftery, and Tantrum (2007) ffix the number of clusters *a priori* and find the $K$ that maximizes integrated likelihood (approximated by the BIC). We can circumvent this model selection problem with a Bayesian nonparametric approach. Note that a $K$ cluster mixture model is just a $K+1$ mixture model with the restriction that $\lambda_{k+1} = 0$. In this sense, every size $K' < K$ mixture model is embedded in the $K$ cluster mixture model. If we let $K \to \infty$ and can find a suitable prior for $\{\lambda_k\}_{k\geq 1}$, then our model will account for every possible configuration of $K$. Then, by treating the number of mixture components as a random variable, we can correctly make probabilistic statements about the number of clusters, including finding the *a posteriori* most likely number of clusters.

To implement an infinite mixture model, we can adopt a Dirichlet process (DP) prior for the distribution that generates the latent positions $Z_i$. The Dirichlet process is a distribution over distributions that was first formalized by Ferguson (1973). Antoniak (1974) showed the DP can be used as prior over distributions in mixture models. Formally, let $(\Theta, \mathcal{F}, G_0)$ be some measurable space and let $\{A_i\}_{i=1}^r$ be a finite measurable partition of $\Theta$. Then for another measure on this space $G$, we say that $G \sim \text{DP}(\alpha, G_0)$ if for some $\alpha > 0$ we have $$\{G(A_i)\}_{i=1}^r \sim \text{Dirichlet}(\{G_0(A_i)\}_{i=1}^r).$$ Note that for some measurable $A \subset \mathcal{F}$ we have 
\begin{align*}
    (G(A), G(A^c)) \sim \text{Dirichlet}(\alpha G_0(A), \alpha G_0(A^c)), 
\end{align*}
and thus
\begin{align*}
    \E_{\alpha,G_0}[G(A)] &= \frac{\alpha G_0(A)}{\alpha G_0(A) + \alpha G_0(A^c)} = G_0(A) \tag{$G_0(A) + G_0(A^c) = 1$} \\
    \Var_{\alpha,G_0}(G(A)) &= \frac{G_0(A)[1 - G_0(A)]}{1 + \alpha}.
\end{align*}
So $G_0$, can be thought of as the mean distribution and $\alpha$ controls the strength of concentration around this mean with $G(A) \overset{L_2}{\longrightarrow} G_0(A)$ for any measurable set $A$ as $\alpha \to \infty$. Rewriting the model of Handcock, Raftery, and Tantrum
(2007) to include a DP prior, we have
\begin{align*}
    Z_{i} \;|\; \mu_i, \sigma_i^2 &\sim \mathcal{N}_d(\mu_i, \sigma_i^2\mathbf{I}_d), \\
    (\mu_i, \sigma_i) \;|\; G &\sim G, \\
    G &\sim \text{DP}(\alpha, \mathcal{N}_d(0,\omega^2\mathbf{I}_d) \times \text{Inverse Gamma}(a,b)).
\end{align*}
We can adopt the same prior assumptions as Handcock, Raftery, and Tantrum (2007) by letting $a = 1$, $b = 0.103 / 2$, and $\omega^2 = 2$. 

## Acknowledgment

This post was adapted from a project for STATS 202C at UCLA that I worked on with my colleagues Sophie Phillips and John Baierl. Thanks to Sophie and John for their help!

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
