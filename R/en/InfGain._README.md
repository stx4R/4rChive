# InfGain.

<p align="center"><img src="../assets/InfGain/cover.png" alt="InfGain. cover image" width="100%"></p>

> An algebra inquiry that checked how the high school logarithm function lets a decision tree pick "the best question," by computing every entropy and information gain by hand on the 14 Play Tennis samples. The result was built as a 3D tree model with entropy shown in color

---

## 1. Project Overview

| Item | Details |
|---|---|
| Project | InfGain. (Logarithms and information gain in decision trees) |
| One-line Summary | Followed Shannon entropy → information gain → a complete ID3 tree by hand, showing that Outlook, with the largest information gain (0.246), becomes the root, with Sunny split by Humidity and Rain split by Wind. Proposed "cumulative information gain" from root to leaf as my own metric |
| Period | 2026.06 (second-year Algebra D.D.I.M.-O.P., session 3) |
| Team | Individual inquiry |
| My Role | Topic selection, background research, all hand calculations, designing and building the 3D model, writing the report (8 pages) |
| Contribution | 100% |

In my earlier HIU inquiry, I learned that the maximum number of steps in a binary search is `log₂N`. That got me interested in the fact that logarithms don't just express efficiency; they also play a central role inside AI algorithms. Decision trees are a basic algorithm used everywhere from spam filters to medical diagnosis and credit scoring, and the calculation that decides how to split the data uses the high school logarithm function as is.

**Research question.** What criterion does a decision tree use to pick "the best question," and which property of the logarithm function does that criterion come from?

| Hypothesis | Details |
|---|---|
| 1. Split efficiency | Choosing the feature with the largest information gain as the root classifies the data correctly with fewer splits |
| 2. Properties of the logarithm | In `H = −Σ p log₂ p`, entropy is 0 when the data consists of a single class, and reaches its maximum (1 bit) when two classes are split half and half |

## 2. Tech Stack

This is a hand-calculation inquiry, so instead of tools this section lists the math concepts and materials.

| Category | Details | Use |
|---|---|---|
| Shannon entropy | `H(X) = −Σ p(xᵢ) log₂ p(xᵢ)` | Measures a node's impurity (how mixed it is) in bits |
| Information gain | `IG(S, A) = H(S) − Σ (\|Sᵥ\|/\|S\|) · H(Sᵥ)` | Measures how good a question is by how much entropy drops before and after the split |
| ID3 (Quinlan, 1986) | Choose the feature with the largest information gain and repeat at each child node | Builds the tree recursively |
| Data | Play Tennis (Mitchell, *Machine Learning*, ch. 3), 14 samples, Yes 9 / No 5 | 4 features: Outlook · Temperature · Humidity · Wind |
| Models | Physical model of balls and toothpicks, Blender model | Shows entropy as color and cumulative information gain as labels |

**Why base 2.** Entropy can be read as "how many yes/no questions, on average, it takes to classify one sample." If the class is already certain, no question is needed, so `H = 0`; if it's half and half, one question is needed, so `H = 1 bit`. It's the same principle as the binary search in the HIU inquiry, which halves the candidates each time and finds the answer in `log₂n` steps.

## 3. Key Features & Contributions

### Calculation Procedure

1. Compute the root entropy `H(S)` of the full dataset
2. Compute the information gain of each of the four features → choose the largest as the root
3. Repeat the same calculation with the remaining features on each branch of the root to complete the tree
4. Check that the chosen structure classifies the data with the fewest splits

**Root.** With Yes 9 · No 5, `H(S) = −(9/14)log₂(9/14) − (5/14)log₂(5/14) ≈ 0.940`. Close to 1, so impurity is high.

| Feature | By branch (Yes : No → H) | Information gain |
|---|---|:---:|
| **Outlook** | Sunny 2:3 → 0.971 · Overcast 4:0 → **0** · Rain 3:2 → 0.971 | **0.246** |
| Humidity | High 3:4 → 0.985 · Normal 6:1 → 0.592 | 0.151 |
| Wind | Weak 6:2 → 0.811 · Strong 3:3 → **1.000** | 0.048 |
| Temperature | Hot 2:2 → **1.000** · Mild 4:2 → 0.918 · Cool 3:1 → 0.811 | 0.029 |

**Second split.**

| Branch | Information gain of the remaining features | Choice |
|---|---|---|
| Sunny (2:3, H = 0.971) | Humidity **0.971** · Temperature ≈ 0.571 · Wind ≈ 0.020 | Humidity (High → all No, Normal → all Yes) |
| Rain (3:2, H = 0.971) | Wind **0.971** · Humidity 0.020 · Temperature ≈ 0.020 | Wind (Weak → all Yes, Strong → all No) |

```
                     [ Outlook ]  H=0.940, IG=0.246
                    /      |      \
              Sunny/   Overcast    \Rain
                  /         |        \
         [ Humidity ]     YES      [ Wind ]
          H=0.971         H=0       H=0.971
          IG=0.971      (n=4)       IG=0.971
          /      \                  /     \
      High      Normal          Weak      Strong
       |          |               |         |
      NO         YES            YES        NO
```

### 3D Model

<p align="center"><img src="../assets/InfGain/model.png" width="90%" alt="Render of the 3D decision tree model"></p>
<p align="center"><sub>A render of the Blender model left on my machine (the name and student ID on the base are hidden). Red spheres are internal nodes that need another split; blue spheres are leaf nodes where classification into a single class is complete</sub></p>

| Model element | Meaning |
|---|---|
| Sphere / toothpick | Node / split |
| Red sphere | Entropy > 0: an internal node whose classification isn't finished yet |
| Blue sphere | Entropy = 0: a leaf node fully classified into a single class |
| Node label | Feature name, that node's `H`, the `IG` at that point |
| Leaf label | Number of samples reached `n`, **cumulative information gain** from the root |

It differs from a flat diagram in two ways. Coloring each node's entropy red or blue makes impurity and complete classification visible at a glance. And instead of the per-node information gain, each leaf carries the sum of information gains from the root down, so the total information and depth each path needs to reach complete classification can be compared directly.

### Main Tasks

- Summarized the background on entropy, information gain and ID3, and interpreted the meaning of base 2 (the connection to binary search)
- Calculated the whole process by hand through the root and the second split; built an entropy reference table
- Designed the cumulative information gain metric; designed and built the 3D model
- Wrote the report

## 4. Results & Metrics

### Quantitative Results

| Path | Splits | Cumulative information gain | Reaches |
|---|:---:|:---:|---|
| Overcast | **1** | **0.246** | Blue leaf (Yes, n = 4) |
| Sunny → Humidity | 2 | 1.217 | Blue leaves (No / Yes) |
| Rain → Wind | 2 | 1.217 | Blue leaves (Yes / No) |

| Item | Result |
|---|---|
| Hypothesis 1 | Supported. With Outlook, the feature with the largest information gain, at the root, the Overcast branch (4 samples) is classified in one step, and the other two branches need just one more split each before every leaf has entropy 0 |
| Hypothesis 2 | Supported. Overcast · Rain-Weak · Rain-Strong, each made up of a single class, have `H = 0`; Wind-Strong · Temperature-Hot, split half and half, have `H = 1.000` |
| Recomputed in code (2026.09) | The tree structure and ranking match the hand calculation. The root information gains are Outlook 0.247 and Humidity 0.152, each off by 1 in the third decimal place from the hand calculation (0.246, 0.151) (see §5-③ below) |
| Assessment Score | (to be confirmed) |

**Entropy reference table (Yes : No)**

| Composition | 0 | 6:1 | 3:1 · 6:2 | 4:2 | 9:5 | 2:3 · 3:2 | 3:4 | 2:2 · 3:3 |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Entropy | **0** | 0.592 | 0.811 | 0.918 | 0.940 | 0.971 | 0.985 | **1.000** |

("0" covers one-sided cases like 4:0, 0:2 and 3:0)

### Qualitative Results

- **Confirmed through calculation that the entropy formula is the logarithm function at work.** The properties of the logarithm line up exactly with those of an impurity measure: "0 for a single class, maximum for half and half."
- **Came to understand the logarithm not as a function that speeds up calculation but as a tool for measuring uncertainty.** Reading base 2 as "number of questions" puts binary search and decision trees in the same language.
- **Got a hands-on feel for what "AI learns" actually means.** It was a mathematical process of comparing entropy at every split and repeatedly making the choice that carries the most information.

### Known Limitations

- **It's a 14-sample textbook example.** Continuous features, missing values and overfitting weren't covered.
- **It wasn't compared with other split criteria.** Comparison with CART's Gini impurity and C4.5's information gain ratio is left for follow-up work.
- **The Wind node label in the Blender model reads `IG=0.048`.** That's Wind's information gain as seen from the root; Wind's information gain on the Rain branch is 0.971. Whether the physical model used the same label is (to be confirmed)
- **The Blender model's legend describes red as "entropy 0.9 or higher."** That differs from the report's definition (entropy > 0). In this tree every internal node is at 0.94 or above, so the result is the same.

## 5. Troubleshooting

### ① The numbers alone didn't show how the hypothesis played out in the tree

**Problem** After computing every entropy and information gain in tables, there were too many numbers and they felt abstract. It was hard to see how the hypothesis "putting the feature with the largest information gain at the root means fewer splits" actually showed up in the tree structure.

**Cause** A table only lines up the values for each feature side by side; it doesn't show which branch of the tree those values finish off, or how quickly.

**Solution** Built a 3D model instead of a flat diagram. Nodes are spheres and splits are toothpicks; leaves with entropy 0 are painted blue, and internal nodes that are still mixed are painted red.

**Result** The colors alone showed the Overcast branch reaching a blue leaf in one step, while Sunny and Rain had to pass through one more red node before reaching blue leaves.

### ② Per-node information gain alone couldn't compare paths

**Problem** A standard decision tree diagram labels each node with only the information gain at that spot. That doesn't let you compare "which path needed more information to finish classifying."

**Cause** Information gain is the effect of a single split, so the cost of a whole path has to be added up in your head.

**Solution** Labeled each leaf with the "cumulative information gain," the sum of information gains from the root to that leaf.

**Result** Overcast 0.246 (1 split) vs. Sunny · Rain 1.217 (2 splits): the depth and required information of each path can be compared with a single number.

### ③ Hand and code calculations differed in the third decimal place (re-verification)

**Problem** While writing this report I recomputed the same data in code, and the root information gains came out as Outlook 0.247 and Humidity 0.152. The hand-calculated values were 0.246 and 0.151.

**Cause** The hand calculation computed the weighted sums from branch entropies already rounded to three decimal places (0.971, 0.985, etc.). Rounding errors in the intermediate values piled up in the last digit.

**Solution** (recorded in report v2.0) Confirmed that neither the ranking nor the tree structure is affected, and listed both values. For hand calculation, keeping one more digit in intermediate values and rounding only at the end avoids this.

**Result** The order Outlook > Humidity > Wind > Temperature and the second split (Humidity, Wind) are identical between the hand calculation and the code.

## 6. Links & Deliverables

| Category | Link |
|---|---|
| GitHub Repository | None |
| Deliverables | Inquiry report (8 pages), 3D decision tree model, Blender model (`.blend`) |
| Related inquiry | HIU inquiry (binary search and `log₂N`) |

<details>
<summary><b>Terms</b></summary>

- **Information content `I(x) = log₂(1/p)`**: a value that grows as an event becomes less likely
- **Shannon entropy**: the expected value of information content. Measures the impurity of data in bits
- **Bit**: the unit of information corresponding to one yes/no question
- **Information gain (IG)**: the drop in entropy from before to after a split. The larger it is, the better the question
- **ID3**: an algorithm created by Ross Quinlan in 1986 that builds a tree recursively using information gain
- **Root / internal / leaf node**: the starting point of the tree / a node that needs further splitting (H > 0) / a node whose classification is finished (H = 0)
- **Cumulative information gain**: the sum of the IG of each split from the root to a leaf. This inquiry's own metric
- **Gini impurity / information gain ratio**: the split criteria used by CART and C4.5, respectively

</details>

<details>
<summary><b>References</b></summary>

- Jay Wengrow, *A Common-Sense Guide to Data Structures and Algorithms* (Korean edition), trans. Lee Min-seok, Hanbit Media, 2018 (pp. 85–92)
- Tom M. Mitchell, *Machine Learning*, McGraw-Hill, 1997 (Chapter 3, Decision Tree Learning)
- J. R. Quinlan, "Induction of Decision Trees," *Machine Learning*, vol. 1, 1986
- Wikipedia, "Entropy (information theory)", "Information gain (decision tree)"

</details>
