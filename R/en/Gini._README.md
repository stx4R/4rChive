# Gini.

<p align="center"><img src="../assets/kr/Gini/cover.png" alt="Gini. cover image" width="100%"></p>

> A two-stage inquiry that used calculus to audit whether a "blind" income classifier, trained without gender, still reproduces the gender gap. It builds a metric that measures discrimination with a definite integral, then differentiates that metric to derive, analytically, a decision threshold that weighs accuracy and fairness together

---

## 1. Project Overview

| Item | Details |
|---|---|
| Project | Gini. (Assessment ② 「지니계수가 숨긴 차별」 ("The Discrimination the Gini Coefficient Hides") → Assessment ③ 「공정성 적분 감사기」 ("The Fairness Integral Auditor")) |
| One-line Summary | Showed that the Gini coefficient can't see average gaps between groups and proposed `D`, the area between two Lorenz curves. Then derived the optimal threshold `t*` from the first-order condition of `J(t) = Acc(t) − λD(t)` and matched a grid search on real data to within 1.9% accuracy |
| Period | 2026.06 (second-year Calculus performance assessments ② · ③, mathematical analysis summary) |
| Team | Individual inquiry |
| My Role | Topic design, deriving the formulas, Python analysis pipeline, interpreting the results, writing the reports and poster |
| Contribution | 100% |

While computing Lorenz curves and Gini coefficients with definite integrals on a worksheet, I started wondering whether this tool, which compresses inequality into a single number, could be applied to AI too. **Can the fairness of an AI model be measured this way? And can that measurement be trusted?**

As automated AI screening spreads through lending, hiring and welfare eligibility, the "digitally disadvantaged" problem is growing: algorithms structurally judging certain groups unfavorably. The common fix is to leave protected variables like gender out of training. But if other variables are correlated with gender, gender information gets in indirectly through them. So I concluded that a post-hoc audit tool that picks apart the model's output directly before deployment is needed.

| Hypothesis | Details | How it's tested |
|---|---|---|
| 1. Proxy variables | Even without the protected attribute, the model produces results close to the real-world gap | Group-wise mean model scores vs. real base rates |
| 2. Mean invariance | A single Gini coefficient is insensitive to average gaps between groups and misjudges a discriminatory model as fair | Response of the pooled Gini coefficient |
| 3. Integral gap index | The area `D` between two Lorenz curves catches the gap Gini misses, and the closed form agrees with numerical integration | Closed form vs. trapezoidal integration |
| 4. Derivative-based optimization | The analytic solution of `J′(t*) = 0` performs as well as the grid search optimum | Accuracy difference at the two `t*` |
| 5. Trade-off | Reaching full fairness through the threshold alone saturates `t*` at its upper bound, making the classifier useless | λ sweep |

## 2. Tech Stack

| Category | Technology | Why |
|---|---|---|
| Data | UCI Adult (Census Income), 32,561 records; after removing missing values, 21,113 for training · 9,049 for validation (7:3 stratified split) | A public benchmark for predicting whether annual income exceeds $50K. Works as a stand-in for automated screening that judges people |
| Model | scikit-learn Logistic Regression (standardization + one-hot, gender and race excluded), validation AUC 0.900 | The score `s = P(income > 50K)` comes out as a probability, and its logit is close to normal, which makes it easy to interpret |
| Math | Definite integrals (Lorenz, selection rate, accuracy), the fundamental theorem of calculus, the chain rule, the logit-normal distribution | Define a metric as an accumulated quantity, and one differentiation leaves only the density at the boundary |
| Numerical methods | SciPy `brentq` (bracketing root finder), `norm`, grid search | Directly exploits the fact that the first-order condition is a continuous function that changes sign near `t*` |
| Visualization | matplotlib (4 plots: densities, gaps, `J(t)`, frontier) | Double-checks the math results visually |

## 3. Key Features & Contributions

### Setup

- The high-income score `s` the model gives each person is treated as a resource being distributed. Putting scores in place of income as the thing distributed lets income inequality metrics carry over directly to AI auditing.
- Groups are split by gender: A (male, population share 0.680, base rate 0.315) · B (female, 0.320, 0.108). This **base rate gap of +0.207** later turns out to be the root of every gap.

### Phase 1 — Measuring with Definite Integrals

```
G = 2 ∫₀¹ ( x − L(x) ) dx,    L(x) = xⁿ  ⇒  G = (n − 1)/(n + 1),   n = (1 + G)/(1 − G)
D = ∫₀¹ | L_M(x) − L_F(x) | dx = | 1/(n_M + 1) − 1/(n_F + 1) |
```

I sorted and accumulated each group's scores, computed `G` with trapezoidal integration, and solved back for `n`. The geometric definition, the area between the curves, reduces to a closed form under the `xⁿ` model, so the numerical integral and the analytic solution can check each other.

### Phase 2 — Fixing with Derivatives

```
SR_g(t) = ∫ₜ¹ f_g(s) ds,   Δ(t) = SR_A(t) − SR_B(t),   D(t) = Δ(t)²
Acc(t)  = Σ_g π_g [ p_g ∫ₜ¹ f_{g,1} ds + (1 − p_g) ∫₀ᵗ f_{g,0} ds ]

J′(t) = Acc′(t) + 2λ Δ(t) [ f_A(t) − f_B(t) ] = 0
```

- Both selection rate and accuracy are defined as definite integrals of the score density, so differentiating them with the fundamental theorem of calculus **leaves only the density value at the boundary `t`.** `D′` combines the chain rule and the fundamental theorem.
- At λ = 0 the first-order condition reduces to the classic **Bayes threshold**, which serves as a sanity check on the model.
- Assuming the logit is normally distributed (logit-normal), the selection rate closes to `Φ((μ − logit t)/σ)`. If the class-conditional densities don't depend on the group, then **`D ∝ (p_A − p_B)²`**; in other words, the formula itself shows that the gap comes from the difference in base rates.

| Group · class | μ | σ |
|---|:---:|:---:|
| A1 (male · positive) | +0.78 | 2.39 |
| A0 (male · negative) | −2.63 | 2.12 |
| B1 (female · positive) | +0.47 | 2.39 |
| B0 (female · negative) | −3.58 | 1.74 |

### Analysis Pipeline

Blind LR training → logit-normal fit per group and class → analytic solution of `J′(t*) = 0` (`brentq`) → grid search on real data → λ sweep and visualization. A single script (206 lines) runs the whole process and writes the results to `results.json`.

### Main Tasks

- Argued the mean invariance of the Gini coefficient and designed the gap index `D`
- Defined selection rate and accuracy as integrals, derived the first-order condition using the FTC and the chain rule, specialized it to the logit-normal case, and derived the corollary `D ∝ (p_A − p_B)²`
- Python analysis pipeline and cross-checking of the analytic solution against grid search
- Wrote the reports (Assessment ②: 3 pages, Assessment ③: 8 pages) and the mathematical analysis summary poster

## 4. Results & Metrics

### Quantitative Results

<p align="center"><img src="../assets/kr/Gini/phases.png" width="95%" alt="Lorenz curves and accuracy-fairness frontier"></p>
<p align="center"><sub>Graphs redrawn from the figures in the original report. Left: the area D between the two groups' Lorenz curves. Right: the frontier where both the gap and accuracy shrink as λ grows</sub></p>

**Phase 1**

| Group | Actual > 50K | Mean model score | Gini (within group) | `n` |
|---|:---:|:---:|:---:|:---:|
| Male | 30.4% | 30.1% | 0.503 | 3.02 |
| Female | 10.9% | 11.5% | 0.624 | 4.32 |

- Treating everyone as one pool leaves a single number, a pooled Gini of 0.569, with no trace of gender discrimination.
- Splitting by group newly reveals an **average gap of 18.6%p** and a **gap index `D` = 0.064**. It agreed with the closed-form value at the 0.06 level.
- The model's `D` (0.064) was smaller than the `D` computed from real base rates (0.097).

**Phase 2 (λ sweep)**

| λ | `t*` analytic | `t*` grid search | Acc @ analytic | Acc @ grid | \|ΔAcc\| | DP gap |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 0 | 0.5633 | 0.4703 | 0.8442 | 0.8478 | 0.0036 | +0.1491 |
| 1 | 0.7041 | 0.6069 | 0.8300 | 0.8436 | 0.0136 | +0.0987 |
| 3 | 0.8407 | 0.7485 | 0.8044 | 0.8235 | 0.0191 | +0.0529 |
| 10 | 0.9516 | 0.8880 | 0.7806 | 0.7956 | 0.0149 | +0.0270 |
| 30 | 0.9800 | 0.9851 | 0.7710 | 0.7700 | 0.0010 | +0.0182 |
| 100 | 0.9800 | 0.9851 | 0.7710 | 0.7700 | 0.0010 | +0.0182 |

| Item | Result |
|---|---|
| Bayes reduction check | `Acc′(t*) ≈ 4.6 × 10⁻¹⁷` at λ = 0 |
| Analytic vs. grid search | Accuracy difference within 1.9%p for every λ |
| Trade-off | DP gap 0.149 → 0.018 costs accuracy 0.844 → 0.771 (about 7%p); `t*` saturates at 0.98 |
| Hypotheses | All of 1–5 supported |
| Assessment Score | (to be confirmed) |

### Qualitative Results

- **Refuted the belief that "removing sensitive variables makes a model fair" with numbers.** The model trained without gender drew almost exactly the real-world picture: women 11.5% · men 30.1% (actual 10.9% · 30.4%).
- The gap the model produced was about as large as the real one, yet it was invisible in the commonly used metric (pooled Gini).
- Linked the two assessments with a single line: "measure with integrals, fix with derivatives." The first stage showed that the standard metric can't see the discrimination; the second answered where, then, to draw the cutoff.
- Also built a policy decision table. Treating λ as an organization's policy parameter, you can pick from the frontier table how much accuracy to give up for how much fairness.

### Known Limitations

- **The blind model still includes the `relationship` variable.** Its values (Husband · Wife) reveal gender almost directly. The indirect leakage through proxies that hypothesis 1 describes may owe more to this variable than to weaker proxies like marital status or working hours. It needs to be re-run without this variable.
- Part of the difference in `t*` position between the analytic solution and grid search comes from the approximation error of the logit-normal assumption.
- **Threshold adjustment alone can't touch the base rate gap.** It has to be combined with preprocessing (reweighting) or fairness constraints during training.
- Only one fairness criterion, the selection rate gap (Demographic Parity), was optimized. The impossibility theorem, which says it can't be satisfied simultaneously with equal opportunity and equalized odds, wasn't covered.
- Only gender was audited; intersectional gaps with race and age weren't covered.
- The figures in Table 1 are embedded as an image in the report, so I couldn't recheck them against the raw data this time. The script survives, but the data file (`adult.data`) isn't on my machine, so it wasn't re-run. (to be confirmed)

## 5. Troubleshooting

### ① Discrimination was invisible to the Gini coefficient

**Problem** Computing the pooled Gini coefficient on the scores of the model trained without gender gave a single number: 0.569. Nothing in that value tells you that women get much lower scores than men.

**Cause** The Gini coefficient measures only how concentrated a distribution is. Multiplying every score by `c` leaves `G` unchanged (mean invariance). Even cutting one group's scores in half across the board leaves the Gini exactly where it was.

**Solution** Drew a separate Lorenz curve for each group and defined the area between the two curves as a new metric, `D`. Modeling the curves as `L(x) = xⁿ` gives `D` a closed form, so the trapezoidal numerical integral and the analytic solution can check each other.

**Result** `D` = 0.064 agreed with the closed form at the 0.06 level. `D` exposed the 18.6%p average gap that had been hiding behind the Gini of 0.569.

### ② The analytic `t*` and the grid search `t*` didn't match

**Problem** The `t*` from the analytic solution and the `t*` from grid search on real data drifted quite far apart for small λ. At λ = 3 it was 0.8407 vs. 0.7485.

**Cause** Part of it is the approximation error of the logit-normal assumption, but the bigger reason is that the accuracy objective is flat near its optimum. On a flat peak, different positions have almost the same height (accuracy).

**Solution** Changed the verification criterion: instead of checking whether the positions match, check whether the performance at those positions matches. I compared the accuracy measured at the two `t*` side by side, and also confirmed that at λ = 0 the first-order condition reduces to the Bayes threshold (`Acc′(t*) ≈ 0`).

**Result** The accuracy difference was within 1.9%p for every λ, and at λ = 0 I got `Acc′(t*) ≈ 4.6 × 10⁻¹⁷`. That's evidence from two directions that the derived first-order condition is correct.

### ③ No matter how large λ got, the gap never reached 0

**Problem** Raising λ to 30 or 100, `t*` stopped at 0.98 and the DP gap wouldn't go below 0.018. Meanwhile accuracy alone fell from 0.844 to 0.771.

**Cause** In the logit-normal model, if the class-conditional densities don't depend on the group, `D ∝ (p_A − p_B)²`. The source of the gap is the base rate gap (+0.207), not the model. Wherever the threshold is placed, that constant term doesn't go away.

**Solution** Showed the limits of threshold adjustment as post-processing in the formulas, and stated in the conclusion that "perfect fairness isn't free." I proposed as next steps combining preprocessing (reweighting) with fairness constraints during training.

**Result** Showed quantitatively that pushing toward full fairness with the threshold alone leaves a classifier (`t*` = 0.98) that rejects almost everyone.

## 6. Links & Deliverables

| Category | Link |
|---|---|
| GitHub Repository | None |
| Deliverables | Assessment ② report "The Discrimination the Gini Coefficient Hides," Assessment ③ report "The Fairness Integral Auditor," analysis script (`.py`, 206 lines), mathematical analysis summary poster |
| Data | [UCI Adult](https://archive.ics.uci.edu/dataset/2/adult) |

<details>
<summary><b>Notation</b></summary>

| Symbol | Description |
|---|---|
| `s` | Classifier score `P(income > 50K)`, `s ∈ [0,1]` |
| `t`, `t*` | Decision threshold (1 if `s ≥ t`), optimal threshold satisfying `J′(t*) = 0` |
| `π_g`, `p_g` | Group population share, base rate `P(y=1 \| g)` |
| `f_{g,1}`, `f_{g,0}` | Score density per group and class (positive · negative) |
| `SR_g(t)`, `Δ(t)`, `D(t)` | Selection rate, selection rate difference, gap index `Δ²` |
| `Acc(t)`, `J(t)`, `λ` | Accuracy, objective `Acc − λD`, fairness weight |
| `L(x)`, `G`, `n` | Lorenz curve `xⁿ`, Gini coefficient, back-solved exponent |
| `μ`, `σ` | Parameters of the normal fit to the logit `logit(s)` |

</details>

<details>
<summary><b>Terms</b></summary>

- **Mean invariance**: the property that the Gini coefficient doesn't change when every value is multiplied by `c`
- **Proxy variable**: a variable highly correlated with a protected attribute that passes on its information indirectly
- **Base rate**: the actual share of positives within a group
- **DP gap**: the difference between two groups' selection rates. 0 means demographic parity
- **Fundamental theorem of calculus (FTC)**: differentiating an integral with respect to its upper or lower limit leaves only the value of the integrand at that boundary
- **Logit-normal distribution**: a distribution on `[0,1]` whose logit-transformed values are normally distributed
- **Bayes threshold**: the classic threshold that maximizes accuracy. The special case λ = 0
- **Accuracy–fairness frontier**: a curve showing accuracy falling as the gap is reduced

</details>

---

+++

## DREAD Risk Assessment

This inquiry proposes a pre-deployment audit tool. The risks of deploying a blind model without an audit are summarized here using DREAD.

| Threat | D | R | E | A | D | Score | Mitigation (the inquiry's recommendation) |
|---|---|---|---|---|---|---|---|
| Gender information leaking in through proxy variables (`relationship`, etc.) | 3 | 3 | 2 | 3 | 1 | 2.4 | Recheck correlation with protected attributes at the data stage and remove strong proxies |
| Misjudging a model as "fair" from the pooled Gini alone | 3 | 3 | 3 | 3 | 1 | 2.6 | Include the integral group-gap metric `D` in audit standards |
| Classifier becoming useless when fairness is forced through the threshold alone | 2 | 3 | 2 | 2 | 2 | 2.2 | Check the cost on the λ frontier first, and combine with preprocessing and in-training constraints |

<sub>D·R·E·A·D = Damage · Reproducibility · Exploitability · Affected users · Discoverability (1~3). The score is the average.</sub>
