# Minimum Wage and Employment

A statistics group project that reproduces the difference-in-differences analysis of **Card and Krueger (1994)** on the effect of a minimum-wage increase on fast-food employment in New Jersey and Pennsylvania.

The project applies the methods from the *Statistical Models* course (Topic 2: Correlation and Linear Regression): OLS estimation, t-tests and p-values, confidence intervals, heteroskedasticity-robust standard errors, and testing averages within the linear regression framework.

## Context

On April 1st, 1992, New Jersey raised its minimum wage from $4.25 to $5.05 per hour (about +19%). Neighbouring Pennsylvania kept it at $4.25. This creates a natural experiment:

- **Treatment group:** New Jersey restaurants
- **Control group:** Pennsylvania restaurants

Card and Krueger surveyed 410 restaurants (Burger King, KFC, Roy Rogers, Wendy's) before the increase (Feb–Mar 1992) and after it (Nov–Dec 1992).

Standard supply-and-demand theory predicts that a binding minimum wage reduces employment of low-paid workers. We test that prediction.

## Research questions

1. Did starting wages increase in New Jersey relative to Pennsylvania?
2. Did full-time-equivalent (FTE) employment decrease in New Jersey relative to Pennsylvania?
3. Does the conclusion survive statistical tests and alternative specifications?

## Repository contents

| File | Description |
| --- | --- |
| `Minimum_Wage_Analysis.ipynb` | Full analysis notebook (Google Colab): sample construction, starting wages, difference-in-differences, linear regression, hypothesis and robustness tests |
| `Code.py` | Data preparation script: reads the fixed-width `public.dat` file using the codebook column positions and replaces missing values (`.`) with `NaN` |

## Data

The analysis uses the original Card and Krueger dataset `public.dat` (410 restaurants: 331 in New Jersey, 79 in Pennsylvania). The file is not included in this repository; place it in the same folder as the notebook before running it.

FTE employment is defined as:

```
FTE = full-time employees + 0.5 × part-time employees + managers
```

## Methods

1. **Sample construction** — parse the fixed-width data with the codebook, recode missing values.
2. **Starting wages** — compare mean starting wages by state, before and after the policy.
3. **Difference-in-differences** — compare the change in mean FTE in New Jersey with the change in Pennsylvania.
4. **Linear regression** on the balanced sample (384 restaurants observed in both waves: 309 NJ, 75 PA):
   - First-difference regression: `Change_FTE = β0 + β1·NJ + ε`
   - Stacked regression (768 observations): `FTE = β0 + β1·NJ + β2·After + β3·(NJ × After) + ε`, with standard errors clustered by restaurant
5. **Hypothesis test** of H0: β1 = 0 with classical and robust (HC1) standard errors.
6. **Robustness checks** — add restaurant-chain fixed effects and company-ownership controls, with HC3 robust standard errors.

## Key results

**Starting wages.** Before the policy, average starting wages were almost identical ($4.61 NJ vs $4.63 PA). Afterwards NJ rose to $5.08 while PA stayed at $4.62 — a relative increase of about **$0.48/hour**. 89% of NJ restaurants ended up paying exactly $5.05, so the policy was binding.

**Employment.** Mean FTE per restaurant:

| State | Before | After | Change |
| --- | --- | --- | --- |
| Pennsylvania | 23.33 | 21.17 | −2.17 |
| New Jersey | 20.44 | 21.03 | +0.59 |

Difference-in-differences: **+2.75 FTE per restaurant** in favour of New Jersey.

**Regression estimates of the NJ effect (balanced sample, N = 384):**

| Specification | Estimate | Std. error | p-value | 95% CI |
| --- | --- | --- | --- | --- |
| First difference, classical SE | 2.75 | 1.15 | 0.018 | [0.48, 5.02] |
| First difference, robust (HC1) SE | 2.75 | 1.34 | 0.040 | [0.13, 5.37] |
| With chain and ownership controls (HC3) | 2.78 | 1.36 | 0.040 | [0.12, 5.45] |
| Stacked regression, clustered SE | 2.75 | 1.34 | 0.040 | [0.13, 5.37] |

The estimate is stable across specifications. We reject "no effect" at the 5% level but not at 1%, and the one-sided test against a negative effect gives p ≈ 0.99.

## Conclusion

The 1992 New Jersey minimum-wage increase raised wages but **did not reduce employment** relative to Pennsylvania — the opposite of what the standard competitive model predicts. The evidence is strong on the sign (no negative effect) but modest on the size: the estimate is close to the 5% threshold and the confidence interval is wide.

### Limitations

- **Parallel trends cannot be tested.** With only one pre-policy survey, we cannot check whether the two states were on similar trends. Most of the effect comes from a decline in Pennsylvania rather than growth in New Jersey, so a Pennsylvania-specific shock would bias the estimate.
- **Small control group.** Only 75 Pennsylvania restaurants are in the balanced sample, which is the main reason the confidence interval is wide.

## Running the notebook

Open `Minimum_Wage_Analysis.ipynb` in Google Colab or Jupyter, upload `public.dat`, and run all cells. Requirements:

```
pandas
numpy
matplotlib
scipy
statsmodels
```

## Reference

Card, D. and Krueger, A. B. (1994). *Minimum Wages and Employment: A Case Study of the Fast-Food Industry in New Jersey and Pennsylvania.* American Economic Review, 84(4), 772–793.
