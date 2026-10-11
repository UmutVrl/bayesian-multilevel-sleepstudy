# bayesian-multilevel-sleepstudy
Bayesian multilevel analysis of sleepstudy data in R using brms, with prior specification, posterior inference, and model diagnostics.

bayesian-multilevel-sleepstudy/
├── README.md
├── LICENSE
├── .gitignore
├── bayesian-multilevel-sleepstudy.Rproj
├── bayesian_sleepstudy_analysis.Rmd
└── bayesian_sleepstudy_analysis.html


TODO (11.10.2026):

1. Prior predictive check	Run the model with sample_prior = "only"	Whether the priors generate plausible RT measurements
2. Sampling diagnostics	Examine R̂, ESS, trace plots, and sampler warnings	Whether the posterior calculations are reliable
3. Frequentist comparison	Fit the identical random-intercept model with lmer()	Compare estimates, uncertainty, and interval interpretations
4. Random slopes	Extend to (1 + Days \| Subject)	Allow participants to differ in daily change
5. Predictive checking	Examine predictions over Days and participants	Check more than the pooled RT distribution
6. Portfolio cleanup	Add comparison tables, dynamic results, limitations, and session information	Turn the learning document into a reusable report
