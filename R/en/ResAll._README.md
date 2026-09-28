# ResAll.

<p align="center"><img src="../assets/kr/ResAll/cover.png" alt="ResAll. cover image" width="100%"></p>

> An Orange study that turns "who should a limited welfare budget go to?" into a classification problem on Costa Rican household poverty data. Alongside the performance of three models, I audited fairness by household-head gender and by region using the separately installed Fairness add-on

---

## 1. Project Overview

| Item | Details |
|---|---|
| Project | ResAll. (Resource Allocation, i.e. allocating welfare resources) |
| One-line Summary | Built a classifier in Orange that picks out vulnerable households from housing, assets, education and household composition alone, with no income data. Logistic regression, the top performer (AUC 0.852), was the worst on gender fairness (DI 0.899) |
| Period | 2026.06 (2nd-year Fundamentals of AI class; 2026.06.08–06.09 according to the working files) |
| Team | Individual study |
| My Role | Problem framing, data cleaning design, building the Orange workflow, installing the Fairness add-on and running the audit, interpreting the results |
| Contribution | 100% |
| Context | Machine learning model implementation assignment (assessment method to be confirmed) |

Welfare budgets are limited, and the poorest households tend to be the ones least able to document their income officially. The Inter-American Development Bank (IADB) tackles this with a Proxy Means Test (PMT): instead of income, poverty is estimated from characteristics you can see, such as wall and roof materials, appliance ownership and education level. This study recasts PMT as a machine learning classification problem and goes as far as checking whether the model systematically misses female-headed or rural households.

| Hypothesis | Details |
|---|---|
| 1. Identifiability | Vulnerable and non-vulnerable households can be told apart from housing, assets, education and household composition alone, with no income variable |
| 2. Metric choice | In welfare, missing a household that needed help (a false negative) costs more, so Recall and F1 matter more than accuracy |
| 3. Where bias starts | The data itself carries group bias before any model is built |
| 4. Trade-off | Mitigating bias gains fairness at the cost of some accuracy, and multiple fairness criteria can't all be satisfied at once |

## 2. Tech Stack

| Category | Technology | Why |
|---|---|---|
| Tool | Orange Data Mining (widget-based workflow) | Connects preprocessing → training → evaluation without code, and the data at each step can be inspected right away |
| Fairness | Orange **Fairness add-on** (not in the default install): As Fairness Data, Dataset Bias, Reweighing, Weighted Logistic Regression | Fairness metrics (DI · SPD · EOD · AOD) show up in the same table as the performance metrics |
| Data | Kaggle "Costa Rican Household Poverty Level Prediction" (IADB) `train.csv`, 9,557 people · 142 variables | Released to improve the PMT that is actually used to select welfare recipients |
| Models | Decision tree, random forest, logistic regression | A rule-based model, an ensemble and a linear probabilistic model differ in character, which makes them good to compare |

## 3. Key Features & Contributions

<p align="center"><img src="../assets/kr/ResAll/workflow.png" width="85%" alt="Orange workflow"></p>
<p align="center"><sub>The Orange workflow as captured at the time. The top row is preprocessing, the middle is training and evaluation, and the bottom left is the Fairness add-on (Dataset Bias, Reweighing → Weighted Logistic Regression)</sub></p>

### Preprocessing

| Stage | Widget | What it does |
|:---:|---|---|
| ① | Select Rows | Keeps only household-head rows (`parentesco1 = 1`) so that one row = one household (9,557 people → 2,973 households) |
| ② | Edit Domain | Merges the 4 `Target` levels (extreme poverty, moderate poverty, vulnerable, non-vulnerable) into vulnerable (1–3) / non-vulnerable (4), and labels `male` as male/female |
| ③ | Select Columns | Keeps only about 20 interpretable variables. `edjefe` · `edjefa` · `dependency`, which mix text and numbers, and the identifiers `Id` · `idhogar` are moved to meta to prevent leakage |
| ④ | Impute | Fills missing values with the mean or the mode |
| ⑤ | As Fairness Data | Protected attribute = household-head gender, privileged group = male, favorable outcome = non-vulnerable |
| ⑥ | Data Sampler | 70% stratified sample (fixed seed) → 2,082 train / 891 test |

| Variable group | Variables |
|---|---|
| Housing | `epared1~3` (walls), `etecho1~3` (roof), `eviv1~3` (floor), `abastaguadentro` (piped water), `noelec` (no electricity), `v14a` (toilet) |
| Crowding & size | `rooms`, `overcrowding`, `hacdor`, `tamhog`, `hogar_nin`, `hogar_mayor` |
| Assets | `refrig`, `computer`, `television`, `qmobilephone`, `v18q` |
| Education & demographics | `meaneduc`, `escolari`, `age`, `male` |
| Region | `area1` (urban) · `area2` (rural), `lugar1~6` (regions) |

### Models

| Model | Characteristics |
|---|---|
| Decision tree | Branches by repeatedly asking the question with the highest information gain. A person can read which factors separate the poor from the rest |
| Random forest | Majority vote of many trees, each built from a different subset of the data and variables. Stable, but harder to interpret |
| Logistic regression | Feeds a weighted sum into a sigmoid to output a poverty probability. The coefficients even show which direction each factor pushes |

### Fairness Audit

- Measured the bias of the data itself with **Dataset Bias**, **before** building any model.
- Compared performance metrics and fairness metrics (DI · SPD · EOD · AOD) in a single table in **Test and Score**.
- Built a bias-mitigated model by attaching **Reweighing** as a preprocessor to **Weighted Logistic Regression**.
- Analyzed region (urban vs. rural) in a separate Orange file with the protected attribute switched.

### Main Tasks

- Framed welfare allocation as a PMT-based binary classification; designed the target binarization and the household-level normalization
- Built the preprocessing workflow, including removing leaky variables, handling missing values and stratified splitting
- Installed the Fairness add-on, set the protected attribute, privileged group and favorable outcome, and compared performance and fairness side by side
- Built the bias-mitigated model and interpreted the results

## 4. Results & Metrics

### Quantitative Results

<p align="center"><img src="../assets/kr/ResAll/results.png" width="95%" alt="Performance and fairness comparison"></p>
<p align="center"><sub>Chart redrawn from the numbers in the Test and Score capture. The dotted line is the gender DI measured from the data with no model (0.903)</sub></p>

**891 test households, class average**

| Model | AUC | CA | F1 | Prec | Recall | MCC | SPD | EOD | AOD | DI |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Logistic regression** | **0.852** | **0.783** | **0.776** | **0.778** | **0.783** | **0.501** | −0.076 | −0.046 | −0.066 | 0.899 |
| Random forest | 0.829 | 0.746 | 0.738 | 0.738 | 0.746 | 0.413 | −0.050 | −0.017 | −0.045 | 0.933 |
| Decision tree | 0.624 | 0.703 | 0.702 | 0.702 | 0.703 | 0.338 | **−0.027** | **−0.024** | **−0.011** | **0.960** |

**Data bias before training (recalculated from `train.csv` while writing report v2.0, 2,973 household heads)**

| Protected attribute | Favorable outcome (non-vulnerable) rate | DI | SPD |
|---|---|:---:|:---:|
| Household-head gender | Male 68.3% · Female 61.7% | 0.903 | −0.066 |
| Region | Urban 68.7% · Rural 58.2% | 0.847 | −0.105 |

| Item | Result |
|---|---|
| Hypothesis 1 | Supported. CA 0.783, AUC 0.852 with no income variable |
| Hypothesis 3 | Supported. Even with no model, the data's DI is 0.903 for gender and 0.847 for region |
| Hypotheses 2 · 4 | Partly confirmed (see the limitations below) |
| Assessment Score | (to be confirmed) |

### Qualitative Results

- **The accurate model was not the fair model.** On all four fairness metrics, logistic regression, the top performer, was the worst, and the best was the decision tree, the weakest performer. Had I picked a model from the performance table alone, I would have deployed the discrimination along with it.
- **The model carried the data's bias over almost unchanged.** Logistic regression's DI (0.899) is nearly the same as the pre-training data's DI (0.903). The bias wasn't newly created by the algorithm; it started in society and in the data.
- I added an audit stage that wasn't part of the assignment. I tracked down and installed the Fairness add-on, which isn't in the default Orange, and linked data cleaning → model comparison → fairness audit → bias mitigation into one workflow.

### Known Limitations

- **The Recall in the table is not the recall for vulnerable households.** Test and Score was set to "class average," so the table's Recall is the weighted average of the two classes' recall and equals accuracy (CA). To see the "missed vulnerable households" that hypothesis 2 is about, the target class has to be switched to "vulnerable," or the Confusion Matrix has to be checked. (to be confirmed)
- **No results remain for the bias-mitigated model.** Weighted Logistic Regression is connected to Test and Score, but the results capture only shows three models, and the widget displays an info notice. The before/after mitigation numbers are (to be confirmed)
- **The random forest's tree count differs between records.** The earlier report said 10, but the saved workflow has 75. The value at the time of the performance capture is (to be confirmed)
- The model was trained on Costa Rican data, so applying it to another society would mean retraining it for each region and checking fairness on an ongoing basis.
- Predictions should only be used to find people to support. They must not be used for stigmatization or sanctions, and the final decision needs human review.

## 5. Troubleshooting

### ① Using the person-level data as-is counts the same household several times

**Problem** In the raw data, one row is one person (9,557 people), but the poverty level and the housing and asset features have the same value across a household. Training on it as-is would count households with many members several times, and the same household could end up in both the training and the test sets.

**Cause** Welfare support is given per household, but the data was per person.

**Solution** I used Select Rows to keep only household-head rows (`parentesco1 = 1`), so that one row = one household. The identifiers `Id` · `idhogar` and the variables that mix text and numbers were moved to meta so they don't go into training.

**Result** The data came down to 2,973 households, split by stratified sampling into 2,082 train / 891 test with the class ratio preserved.

### ② The "best" model by accuracy was the least fair

**Problem** Going by the performance metrics alone, the answer was simply to pick logistic regression. But how this model treats female household heads doesn't show up in the performance table.

**Cause** Base Orange has no fairness metrics. Accuracy and AUC are overall averages, which hide the differences between groups.

**Solution** I installed the Fairness add-on and used As Fairness Data to set the protected attribute (household-head gender), the privileged group (male) and the favorable outcome (non-vulnerable). Performance and DI · SPD · EOD · AOD then appeared side by side in a single Test and Score table. I also measured the data bias before any model separately with Dataset Bias.

**Result** A single table showed that the performance ranking (logistic > random forest > tree) and the fairness ranking (tree > random forest > logistic) are exact opposites.

### ③ The table's Recall was identical to accuracy (re-verification)

**Problem** While reorganizing the report, I noticed that for all three models Recall matched CA to the third decimal place (0.783, 0.746, 0.703). Hypothesis 2 centered on "how many vulnerable households are missed," but that value wasn't shown anywhere on its own.

**Cause** The target class in Test and Score was set to "None, show average over classes." Recall averaged with class-proportion weights is mathematically equal to accuracy.

**Solution** (Recorded in report v2.0) I confirmed the cause from the original captures and labeled the table's Recall explicitly as "class average." The recall for vulnerable households has to be pulled again with the target class set to "vulnerable."

**Result** Hypothesis 2 (the principle behind the metric choice) still holds, but I noted in the limitations that these numbers can't tell "what percentage of vulnerable households were found."

## 6. Links & Deliverables

| Category | Link |
|---|---|
| GitHub Repository | None |
| Deliverables | Orange workflow (`기계학습 모델 구현.ows`, "Machine learning model implementation"), captures of the widgets, data tables, Data Sampler and Test and Score, a workflow guide document |
| Data | Kaggle "Costa Rican Household Poverty Level Prediction" (IADB) |

<details>
<summary><b>Metrics</b></summary>

| Metric | Meaning |
|---|---|
| CA | Overall accuracy. Vulnerable to class imbalance |
| Precision / Recall | Share of predictions that were correct / share of actual cases that were found |
| F1 | Harmonic mean of precision and recall |
| AUC | Area under the ROC curve. Discriminative power independent of the threshold |
| MCC | Matthews correlation coefficient. A reliable overall metric even on imbalanced data |
| DI | Ratio of the two groups' favorable outcome rates. 1.0 is full parity |
| SPD | Difference between the two groups' favorable outcome rates. 0 is full parity |
| EOD | Difference between the two groups' recall (TPR) |
| AOD | Average of the two groups' TPR and FPR differences |

</details>

<details>
<summary><b>Terms</b></summary>

- **Proxy Means Test (PMT)**: A way of selecting welfare recipients that estimates poverty from observable characteristics such as housing and assets when income is hard to document
- **Stratified sampling**: Splitting a sample while preserving class and group proportions
- **Data leakage**: Variables that can't be known at prediction time, or that carry target information, slipping into training and inflating performance
- **False negative / False positive**: Actually vulnerable but judged non-vulnerable (excluded from support) / actually non-vulnerable but judged vulnerable (over-support)
- **Protected attribute / Privileged group**: The variable along which there must be no discrimination / the group that receives the favorable outcome more often (the reference point for the metrics)
- **Reweighing**: A preprocessing technique that reassigns sample weights before training to correct imbalances between groups

</details>

---

+++

## Customer Journey Map

Here the "user" is the welfare officer who uses the model.

| Stage | What they do | What they think | What the model and audit provide |
|---|---|---|---|
| Survey | Visits households and records housing and assets | "How do I judge a home with no income paperwork?" | A decision from about 20 observable features alone |
| Screening | Pulls support candidates by model score | "Can I trust this list?" | Discriminative power of AUC 0.852 |
| Review | Looks at the makeup of the candidate list | "Are female-headed or rural households being left out?" | Gender DI 0.899; regional DI of 0.847 in the data itself |
| Decision | Finalizes who receives support | "If the model is wrong, who's responsible?" | The principle that predictions are for finding candidates and people make the final call |

## DREAD Risk Assessment

| Threat | D | R | E | A | D | Score | Response |
|---|---|---|---|---|---|---|---|
| Systematically missing vulnerable rural and female-headed households | 3 | 3 | 2 | 3 | 2 | 2.6 | Check recall per group; bias mitigation such as Reweighing |
| Missing the omissions by looking only at class-average metrics | 3 | 3 | 3 | 2 | 1 | 2.4 | Audit with Recall for the target class "vulnerable" and the Confusion Matrix |
| Repurposing predictions for stigmatization or sanctions | 3 | 2 | 2 | 3 | 1 | 2.2 | Limit use to finding support recipients; final human review |

<sub>D·R·E·A·D = Damage · Reproducibility · Exploitability · Affected scope · Discoverability (1~3). The score is the average.</sub>
