# causal-transport-tgd

Causal transportability simulation for estimating ketamine treatment effects in transgender and gender-diverse (TGD) populations with chronic pain.

Implements a constrained prior predictive Monte Carlo estimator of the transported treatment effect ψ, combining a source ketamine cohort with target population baseline distributions via Pearl's transportability framework.

## Estimand

$$
\psi = E_{Z \sim P^*(Z)}\big[E(\Delta \mid Z, S = 0)\big]
$$

where $S=1$ is the source ketamine cohort, $S=0$ is the target TGD population, and $\Delta = Y_{12m} - Y_{\text{baseline}}$.

Identification relies on structural invariance:

$$
E[\Delta \mid Z, S = 1] = E[\Delta \mid Z, S = 0]
$$

so $\psi$ is evaluated using the source response surface.

## Method

Two-layer Monte Carlo:

- **Outer loop (T draws):** sample response surface parameters $(\alpha, B, \sigma)$ consistent with published source summaries — parameter uncertainty
- **Inner loop (M individuals):** simulate target covariates $Z_i \sim P^*(Z)$ and evaluate the conditional mean response — covariate heterogeneity

Because priors are only partially constrained by published moments (not a full likelihood), output is a **constrained prior predictive distribution**, not a Bayesian posterior.

## Key functions

| Function | Purpose |
|---|---|
| `draw_B()` | Sample slope matrix $B$ with optional sign constraints |
| `anchor_alpha()` | Anchor intercept to published source mean change |
| `simulate_target_Z_std()` | Simulate standardized target baseline covariates |
| `simulate_delta()` | Simulate outcome change $\Delta$ given $Z, \alpha, B, \sigma$ |

## Outputs

- Point estimates, 95% intervals, and Monte Carlo SEs for transported pain and depression effects
- Sensitivity sweeps over:
  - target baseline correlation $\rho$
  - prior slope scale `beta_sd`
  - target baseline means $\mu_{\text{pain}}, \mu_{\text{dep}}$ (at $\rho=0$ and $\rho=0.6$), to check whether pain/depression estimates decouple

## File

- `CompFusion.qmd` — main simulation and sensitivity analyses

## Requirements

R with `MASS` (for `mvrnorm`) and `knitr`.

```bash
quarto render CompFusion.qmd
```
