# Bayesian Multilevel Analysis of the Sleepstudy Data

This project demonstrates a Bayesian multilevel modelling workflow in R using **brms** and **Stan**. 
It analyses reaction times from the `sleepstudy` dataset to examine how sleep deprivation affects reaction time and whether this effect varies across participants. 
It is a learning project and is compared with a frequentist `lme4` analysis.

**Rendered report:** https://UmutVrl.github.io/bayesian-multilevel-sleepstudy/bayesian_sleepstudy_analysis.html

The project covers:

- Data exploration
- Weakly informative prior specification and prior predictive checks
- Bayesian random-intercept and random-slope models
- MCMC convergence diagnostics
- Posterior predictive checks
- Prior sensitivity analysis
- Frequentist mixed-model comparison
- LOO model comparison

## Research question

> How does reaction time change across days of sleep deprivation, and do participants differ in their baseline reaction time and daily rate of change?

## Data

The analysis uses the `sleepstudy` dataset from the **lme4** package: 180 observations from 18 participants, with 10 daily measurements each.

- `Reaction`: average reaction time per day (ms)
- `Days`: number of days of sleep deprivation
- `Subject`: participant identifier

## Models

### Random-intercept model

Participants differ in baseline reaction time but share a common daily effect:

$$
\text{Reaction}_{ij} = \beta_0 + u_{0i} + \beta_1 \text{Days}_{ij} + \epsilon_{ij}
$$

```r
Reaction ~ Days + (1 | Subject)
```

### Random-slope model

Participants additionally differ in their daily rate of change:

$$
\text{Reaction}_{ij} = \beta_0 + u_{0i} + (\beta_1 + u_{1i})\text{Days}_{ij} + \epsilon_{ij}
$$

```r
Reaction ~ Days + (Days | Subject)
```

## Priors

Weakly informative priors were chosen from plausible reaction-time scales.

Random-intercept model:

```r
priors_ri <- c(
  prior(normal(300, 100), class = Intercept),
  prior(normal(0, 10), class = b, coef = Days),
  prior(student_t(3, 0, 50), class = sd),
  prior(student_t(3, 0, 50), class = sigma)
)
```

Random-slope model (same priors, plus a prior on the intercept–slope correlation):

```r
priors_rs <- c(
  priors_ri,
  prior(lkj(2), class = cor)
)
```

Prior predictive checks were used to confirm that the priors produce plausible reaction-time distributions before the model sees the data.

## Main results

Under the random-slope model:

- Average reaction time increased by **10.18 ms per day** of sleep deprivation (95% CrI: 6.77–13.44).
- Daily change varied across participants: the between-participant SD was **6.58 ms/day** (95% CrI: 4.18–10.15).
- Baseline reaction time also varied: SD = **26.68 ms** (95% CrI: 15.63–41.95).
- There was no clear association between baseline and daily rate of change (`cor(Intercept, Days)` = 0.07, 95% CrI: −0.46–0.59).

## Prior sensitivity

In the random-intercept model, the daily effect was 10.40 ms/day (95% CrI: 8.82–11.99) under the primary prior and 8.97 ms/day (95% CrI: 7.43–10.48) under a much tighter zero-centred prior (`normal(0, 2)`). The conclusion was unchanged, but the estimate moved toward the prior.

## Model comparison

Approximate leave-one-out cross-validation (LOO) favoured the random-slope model:

| Model | `elpd_diff` | `se_diff` |
|---|---:|---:|
| Random slope | 0.0 | 0.0 |
| Random intercept | −23.2 | 12.1 |

The difference is about 1.9 standard errors (`p_worse` = 0.97), so the preference is clear but not overwhelming. All Pareto-$k$ diagnostics were below 0.7, supporting the reliability of the LOO estimates.

## Bayesian versus frequentist comparison

| Model | Daily effect (ms/day) | Uncertainty |
|---|---:|---|
| Bayesian random intercept | 10.40 | 95% CrI: [8.82, 11.99] |
| Frequentist random intercept | 10.47 | SE = 0.80 |
| Bayesian random slope | 10.18 | 95% CrI: [6.77, 13.44] |
| Frequentist random slope | 10.47 | SE = 1.55 |

With the same model structure, Bayesian and frequentist estimates nearly agree. The wider uncertainty in the random-slope models reflects real uncertainty about the average slope, not a feature of Bayesian methods. Credible intervals and standard errors answer different questions: a credible interval is a probability statement about the parameter, whereas a standard error describes sampling variability under repeated sampling.

## Diagnostics

- `Rhat` and bulk/tail effective sample sizes
- Trace plots
- Posterior predictive checks
- Pareto-$k$ diagnostics for LOO

Posterior predictive checks showed that the random-slope model broadly reproduced the overall reaction-time distribution and most participant trajectories. Some abrupt day-to-day fluctuations were not captured by the linear trend.

## Limitations

- The model assumes linear change over days and normally distributed residuals.
- Only 18 participants, which limits precision of the between-person variance estimates.
- Individual slopes are partially shrunk toward the population average.
- Prior sensitivity was checked for the daily effect in the random-intercept model, not comprehensively for the random-slope model.

## Repository structure

```text
bayesian-multilevel-sleepstudy/
├── README.md
├── LICENSE
├── .gitignore
├── bayesian-multilevel-sleepstudy.Rproj
├── bayesian_sleepstudy_analysis.Rmd
└── bayesian_sleepstudy_analysis.html
```

## How to run

1. Install Rtools (Windows) and the packages `brms`, `rstan`, `tidyverse`, `bayesplot`, `loo`, `tidybayes`, `lme4`, `knitr`.
2. Open the `.Rproj` file in RStudio and knit `bayesian_sleepstudy_analysis.Rmd`. The first knit compiles and samples several Stan models and takes a few minutes.

GitHub shows `.html` files as source code. Open the rendered report through the link at the top (GitHub Pages), or download the file and open it in a browser.

## Author

**Umut Can Vural**, 2026. Released under the MIT License.