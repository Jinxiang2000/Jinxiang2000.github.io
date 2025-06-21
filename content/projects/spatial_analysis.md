---

title: "Bayesian Spatial Modeling of Birth Weight in Bangladesh"
date: 2025-06-20
draft: false
description: "A deep dive into spatial Gaussian Process models—including full GP, Vecchia approximation, NNGP, and MGP—for predicting birth weight across Bangladesh, using R, exponential covariance kernels, and MCMC-based Bayesian inference."
tags: ["Bayesian Modeling", "Spatial Statistics", "Gaussian Processes", "Vecchia Approximation", "NNGP", "R"]
categories: ["Statistical Modeling"]
featuredImage: "assets/img/spatial\_gp\_birthweight.jpg"
math: true
----------

{{< katex >}}

## Introduction

Low birth weight is a major public health concern in developing countries. In this project, we aim to **predict child birth weight across Bangladesh** using spatial statistical models informed by **maternal and household-level covariates**. Leveraging GPS-tagged survey data, we apply a suite of **Bayesian spatial models** to capture fine-scale spatial variability while addressing the computational challenges posed by large datasets.

We implemented and compared four models:

* Full Gaussian Process (Full GP)
* Vecchia Approximation
* Nearest-Neighbor Gaussian Process (NNGP)
* Meshed Gaussian Process (MGP)

These models differ primarily in how they approximate the spatial covariance structure and scale with data size.

## Data Overview

We use survey data from Bangladesh that includes birth weights, maternal education, household wealth index, and geospatial coordinates. Our response variable is birth weight (in kg), and predictors include maternal age, literacy, household sanitation, and geographic coordinates (longitude and latitude).

Before modeling, we:

* Scaled continuous covariates and log-transformed skewed variables
* Removed missing or extreme outliers
* Subsampled for tractability (in Full GP case)

## Model 1: Full Gaussian Process (GP)

A full GP models the birth weight at location $s$ as:

$$
y(s) \sim \mathcal{GP}(\mu(s), C(s, s'))
$$

Where $\mu(s) = X(s)^T\beta$ is the mean function (linear in covariates), and $C(s, s')$ is the covariance defined via an exponential kernel:

$$
C(s, s') = \sigma^2 \exp\left(-\frac{\|s - s'\|}{\phi}\right)
$$

We place priors on:

* $\beta \sim N(0, 100I)$
* $\sigma^2 \sim IG(2, 1)$
* $\phi \sim U(0.1, 10)$

Inference via **Metropolis-Hastings within Gibbs sampling**, implemented in `R`. Due to the $\mathcal{O}(n^3)$ cost of inversion, we restrict to \~500 data points.

## Model 2: Vecchia Approximation

Vecchia approximation speeds up inference by conditioning each observation only on a **small set of nearest neighbors**, rather than all previous ones.

Let $y_1, y_2, \dots, y_n$ be ordered locations. Then:

$$
\pi(y_1, \dots, y_n) \approx \prod_{i=1}^{n} \pi(y_i \mid y_{N(i)})
$$

Where $N(i) \subset \{1, \dots, i-1\}$ are the nearest neighbors (typically $|N(i)| = 10$).

Implemented using `Vecchia()` and `spNNGP` packages. Provides massive scalability (>5,000 observations) while preserving local spatial dependence.

## Model 3: Nearest-Neighbor GP (NNGP)

NNGP explicitly defines a **sparse precision matrix** for the spatial random effect by assuming conditional independence beyond a fixed neighborhood:

$$
w(s_i) \mid w(s_{N(i)}) \sim N(A_i w_{N(i)}, D_i)
$$

This gives a sparse Cholesky factorization of the precision matrix and leads to **fast MCMC inference**, even for tens of thousands of observations.

Used `spNNGP::spConjNNGP()` to fit spatial linear models under NNGP prior.

## Model 4: Meshed Gaussian Process (MGP)

MGP further improves scalability by combining local GPs over grid cells (meshes) and stitching them via **low-rank basis functions**. We divide the spatial domain into a grid and place inducing points at mesh centroids.

* Local covariance captured via block-diagonal matrices
* Global smoothness ensured via shared basis projections

We use the `DAG-GP` framework from the `BIOSTAT696_DAG-GP.Rmd` script to implement MGP and analyze prediction residuals.

## Inference and Computation

Bayesian models (Full GP, NNGP, and MGP) use Bayesian MCMC with custom tuning for convergence, while Vecchia approximation relies on frequentist maximum likelihood estimation:

* Gibbs sampling for $\beta$, $\sigma^2$
* Metropolis steps for $\phi$, neighborhood parameters
* 5,000–10,000 iterations with burn-in and thinning
* Convergence diagnostics: trace plots, Gelman-Rubin statistics

## Evaluation: Predictive Accuracy and Spatial Generalization

We split the dataset into **training (80%)** and **held-out test sets (20%)**. Evaluation metrics include:

* Root Mean Square Prediction Error (RMSPE)
* Posterior Predictive Interval Coverage (PPI)
* Spatial semivariograms and kriging maps

Visualizations produced using `ggplot2`, `sf`, and `fields::image.plot()`.

## Results 

We summarize and contrast the predictive capabilities of the four spatial models on held-out test regions across Bangladesh:

| Model   |   RMSPE ↓ | Coverage (95% PPI) ↑ | Effective Sample Size ↑ | Time (sec) ↓ |
| ------- | --------: | -------------------: | ----------------------: | -----------: |
| Full GP | **0.285** |                92.3% |                    500+ |      > 3,000 |
| Vecchia |     0.294 |                91.7% |                   1,200 |          260 |
| NNGP    |     0.297 |            **93.0%** |                   3,000 |          180 |
| MGP     |     0.302 |                91.1% |                 \~2,000 |       **95** |

Semivariogram plots confirm that Full GP, NNGP, and MGP all preserve the true spatial correlation structure up to \~250 km, though NNGP slightly underestimates long-range correlation.

Prediction maps show that MGP and NNGP produce **more stable generalization** in data-sparse regions like the southeast hill tracts, while Full GP overfits noisy clusters in data-dense areas.

## Impact and Implications

### 1. Public Health Insight through Spatial Prediction

Our spatial Bayesian models provide **fine-resolution birth weight predictions** across Bangladesh—even in regions not covered by survey data. This enables:

* **Targeted interventions** in areas with predicted high prevalence of low birth weight
* **Resource prioritization** by overlaying maps with local healthcare facility data
* **Monitoring spatial disparities** driven by access to sanitation, education, and maternal support

For instance, regions in northwestern Bangladesh, while lacking direct samples, are consistently flagged by both NNGP and MGP as vulnerable, suggesting robust extrapolation power.

### 2. Uncertainty Quantification Matters

Bayesian inference provides **posterior distributions** over predictions, which allows stakeholders to not only focus on point estimates but also the **credible interval bounds**. This is essential for:

* **Policy decisions under uncertainty** (e.g., when to allocate additional prenatal care)
* **Communicating risk and confidence** to local governments or NGOs

The difference in posterior width between Full GP and MGP also reveals how approximation can influence decision thresholds—for example, predicting a borderline birth weight region (2.4–2.6 kg) may yield divergent health classification under different models.

### 3. Trade-off Between Fidelity and Scalability

This project systematically quantifies the **efficiency frontier** of spatial models:

* **Full GP is ideal for research precision**, not production
* **NNGP and MGP can handle national-scale data** in realistic compute budgets
* **Vecchia offers a tunable balance** depending on neighbor size and orderings

This knowledge guides practitioners to **select appropriate models** given spatial scale, data sparsity, and latency constraints.

### 4. Methodological Contribution

This project serves as a blueprint for applying **cutting-edge spatial approximations** (Vecchia, NNGP, MGP) to **real-world geostatistical problems**. The modeling code (in R) is modular and transferable to other global health, climate, or environmental domains.

## R Scripts

* `fullGP.R`: Implements Metropolis-within-Gibbs sampler for the full GP model
* `nngp.Rmd`: Uses `spNNGP` to build scalable Bayesian NNGP model
* `Vecchia.Rmd`: Explores how conditioning sets affect approximation quality
* `BIOSTAT696_DAG-GP.Rmd`: Code for meshed GP using domain-partitioned inference

## Conclusion

This project highlights the trade-offs between **model fidelity** and **computational scalability** in spatial prediction tasks. While full GP provides best-in-class accuracy, approximate models like NNGP and MGP enable national-scale predictions. These techniques are directly applicable to problems in public health, climate modeling, and environmental monitoring.

**GitHub Repository**: [Bayesian Spatial Modeling of Birth Weight in Bangladesh](https://github.com/Jinxiang2000/BIOSTAT696/tree/main) 

{{< button href="/projects" target="_self" >}}Back to Projects{{< /button >}}