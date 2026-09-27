# AGDecision.

<p align="center"><img src="../assets/AGDecision/cover.png" alt="AGDecision. cover image" width="100%"></p>

> A Society and Culture inquiry that tested "is algorithm-based decision-making really objective?" with a questionnaire (15 people) and interviews (6 people). Rather than what algorithms discriminate against, it focuses on why people accept their verdicts without question

---

## 1. Project Overview

| Item | Details |
|---|---|
| Project | AGDecision. (Testing the objectivity of algorithm-based decision-making) |
| One-line Summary | A mixed-methods study combining a questionnaire and interviews that found four patterns in how people trust algorithms: ① the more they understand, the less they trust ② they trust without understanding ③ they hand over even high-stakes decisions ④ they distinguish "objective" from "fair" |
| Period | 2026.06 (second-year Society and Culture performance assessments ① · ②) |
| Team | Individual inquiry |
| My Role | Topic selection, literature review, questionnaire and interview design, data collection, statistical analysis, report writing |
| Contribution | 100% |

Algorithms now handle not just content recommendations but résumé screening, credit scoring, admissions and recidivism prediction. Behind this lies the common belief that "AI isn't swayed by emotion, so it's more objective than people." But algorithms learn from data that people have built up, and they learn the discrimination written into that data along with it.

The starting point was [HowTo](HowTo_README.md), a project of mine. Its model for identifying trash from photos repeatedly misclassified the same object as a different type whenever the camera angle, lighting or background changed, or when a shape rare in the training data came up. If a model gets it wrong even on something as low-stakes as trash, what about algorithms that judge people, in hiring or lending? And what if those misjudgments aren't random but concentrated on particular groups? This inquiry takes that question through the reflective attitude taught in Society and Culture: the habit of questioning what lies behind visible phenomena.

Most prior research showed that algorithm **outputs** are biased. This inquiry explains, from the side of user perception, **why that discrimination is accepted without question**.

## 2. Tech Stack

This is a social science inquiry, so instead of code this section lists the research methods and analysis tools.

| Category | Method · Tool | Why |
|---|---|---|
| Research design | Mixed methods (quantitative + qualitative) | The questionnaire shows "what happens" and the interviews show "why," each filling the other's gaps |
| Quantitative data | Questionnaire: 18 items · 6 areas · 5-point scale (15 students in the same grade) | Measures step by step: abstract attitudes toward objectivity → trust by domain → the gap with awareness of bias |
| Qualitative data | Interviews: semi-structured (6 people) · 4 common questions + follow-ups tailored to each respondent's stance | Draws out the reasoning behind the numbers |
| Analysis | Mean comparison, correlation coefficient, cross-tabulation, Mann-Whitney U test | With a small sample and normality hard to assume, I used a nonparametric test |
| Literature | Cathy O'Neil, *Weapons of Math Destruction*; ProPublica COMPAS; Gender Shades; Amazon's AI recruiting tool; Society and Culture textbook | Backs up, with case studies, the "discrimination in outputs" that a perception survey can't measure directly |

## 3. Key Features & Contributions

### Questionnaire Design (15 people · 18 items)

| Section | Area | What it measures |
|:---:|---|---|
| 0 | Basic information | Gender · time spent using AI · coding experience |
| 1 | Perceived objectivity | "Is AI more objective than humans?", "Is it fair, free of emotion and prejudice?" |
| 2 | Trust by domain | Hiring · lending · admissions · recidivism prediction · content recommendation |
| 3 | Awareness of bias and discrimination | Possibility of working against particular groups, awareness of discrimination cases |
| 4 | Perception gap | "Do you trust it even knowing it's biased?", "Should it be corrected if it discriminates?" |
| 5 | Open response | The reason for trust or distrust in one sentence |

### Interview Design (6 people)

I first asked 4 common questions (degree of trust in AI, agreement that it's objective, understanding of the decision process, possibility of discrimination against particular groups), then asked different follow-up questions depending on the respondent's stance. The respondents fell into three types.

| Type | Core reasoning |
|---|---|
| Partial trust (2 people) | **Trust by elimination.** "An AI with no personal agenda is probably better than opaque human subjectivity." Their reasons for using it are closer to avoiding responsibility and convenience than to trust |
| Strong trust (2 people) | **Statistical optimum.** They believe the average over vast amounts of data filters out individual prejudice, and read discrimination cases as temporary errors rather than flaws in the technology |
| Strong distrust (2 people) | **Mechanical indifference.** They believe a system that copies unequal real-world data can't be objective, and point out that marginalized groups lack data in the first place, so the mainstream's criteria become the standard |

### Safeguards for Objectivity

- The questionnaire mixed positive and negative statements, used a standardized 5-point scale, and left out wording that would steer respondents toward a particular answer.
- In the interviews, I asked neutral questions without revealing my hypothesis and recorded the answers verbatim.

### Main Tasks

- Set the inquiry topic and formed the hypothesis ("algorithms learn and amplify society's biases, reproducing discrimination under the appearance of objectivity")
- Literature review, connected to course concepts (objective attitude and intersubjectivity, the information gap, critical criminology)
- Designed the 18-item questionnaire and the interview questions, collected data, ran the statistical analysis and classified respondent types
- Interpreted the quantitative and qualitative results against the literature and wrote the report (11 pages)

## 4. Results & Metrics

### Quantitative Results

<p align="center"><img src="../assets/AGDecision/findings.png" width="95%" alt="Perception items and trust by domain"></p>
<p align="center"><sub>Graphs redrawn from the figures in the report. Left: "black-box trust," with understanding low compared with perceived objectivity and trust. Right: "high-stakes delegation," with recidivism prediction trusted more than hiring, lending or admissions</sub></p>

| Pattern | Evidence |
|---|---|
| ① The more they understand, the less they trust | Coding-experienced (5 people): understanding 4.0 · trust 2.8; no coding experience (10 people): understanding 1.8 · trust 4.1. Understanding–trust correlation coefficient −0.26. Mann-Whitney U: difference in understanding p = 0.013 (significant), difference in trust p = 0.152 (same direction but not significant) |
| ② They trust without understanding | "AI is objective" 4.13, "It can be trusted" 3.67, "I understand how it works" 2.53 |
| ③ They hand over even high-stakes decisions | Trust by domain: content recommendation 4.40 > recidivism prediction 3.73 > hiring 3.27 > lending 3.20 > admissions 3.00 |
| ④ Objective ≠ fair | "It is objective" 4.13 vs. "It is fair" 3.07. 6 of the 15 rated objectivity at least 2 points higher than fairness. "It should be corrected even if it discriminates" 3.73 (6 people gave it a 5) |

| Item | Result |
|---|---|
| Awareness of possible discrimination / direct experience of unfairness | 3.53 / 5 people |
| Open responses | 4 kinds of reasons for trust (consistency, statistical rationality, processing power, trust in data) and 5 kinds of reasons for distrust (opacity, misinformation, lack of accountability, lack of context and ethics, potential for abuse) |
| Assessment Score | (to be confirmed) |

### Qualitative Results

- **Cross-checked the four patterns against the interviews and the literature.** The pattern of trusting less with more understanding (①) lines up with interviewees at an intermediate coding level saying "the detailed logic is a black box," and it led to the interpretation that the algorithmic opacity O'Neil describes shows up on the perception side as trust "because they don't understand" (②).
- **Pointed out the flaw in "more data means fairness" using a textbook case.** The *Literary Digest* poll surveyed ten million people and still got its prediction wrong, because low-income people were missing from the sample. I countered the strong-trust group's reasoning with the problem of data representativeness and connected it to the underrepresentation errors in Gender Shades.
- **Found that the grounds for trust and distrust differ in kind.** Reasons for trust were vague expectations of "consistency, efficiency, statistics," while reasons for distrust were concrete observations of "opacity, lack of context."
- **Proposed alternatives along three lines.** Algorithm literacy education; mandatory explainability and final human review in high-stakes domains; and representative training data with discrimination impact assessments.

### Known Limitations

- **The sample is only 15 male students in the same grade.** The inquiry deals with discrimination by gender and class, yet the sample itself is skewed, which makes the conclusions hard to generalize.
- **Only perceptions were measured.** Discrimination in actual algorithm outputs wasn't measured directly; it was supported with cases from the literature.
- **Pattern ① is a tendency.** There were only 5 coding-experienced respondents, and the difference in trust wasn't significant.
- In the submitted version, reference [2](Society and Culture textbook) still has blank author, publisher and year fields. (to be confirmed)

## 5. Troubleshooting

### ① I needed a way to compare 5 people with 10

**Problem** Splitting by coding experience puts 5 people on one side and 10 on the other. The responses are on a 5-point scale, so the distribution is far from normal.

**Cause** Comparing means alone lets a few extreme responses swing the results a lot. The sample was small and normality was hard to assume.

**Solution** First compared each group's response distribution with cross-tabulation, then checked the difference between the two groups with the Mann-Whitney U test, a rank-based nonparametric test.

**Result** The difference in understanding was significant (p = 0.013); the difference in trust pointed the same way but wasn't significant (p = 0.152). In the cross-tabulation, more than half of the experienced group showed low trust, while 80% of the inexperienced group showed high trust.

### ② The conclusion I wanted wasn't confirmed statistically

**Problem** The difference in trust, the core of pattern ① ("the more they understand, the less they trust"), which fit the hypothesis best, didn't reach significance.

**Cause** With only 5 people in the experienced group, there isn't enough statistical power even if a difference exists.

**Solution** Toned down the conclusion. Pattern ① is written up not as a "confirmed result" but as "a tendency to recheck with a larger sample," with the correlation coefficient (−0.26) and interview statements attached as supporting evidence. It's also listed separately in the limitations section.

**Result** Readers can now tell apart what was confirmed by testing (the difference in understanding for ①), what rests on mean comparisons (②③④), and what remains a tendency (the difference in trust for ①).

### ③ A perception survey alone couldn't say whether algorithms "actually discriminate"

**Problem** The hypothesis is that "algorithms reproduce discrimination," but the questionnaire and interviews measure only people's perceptions.

**Cause** A high school student has no way to obtain and measure the outputs of hiring or recidivism prediction algorithms directly.

**Solution** Split the roles. Discrimination in outputs is backed by the COMPAS, Gender Shades and Amazon recruiting cases, and this inquiry concentrates on explaining, at the level of perception, "why that discrimination gets accepted." As follow-up work, I designed an experiment on the HowTo model comparing misrecognition rates between categories with many training images and categories with few, to test at the technical level that "the less data there is on something, the more often the model gets it wrong."

**Result** The scope of the hypothesis test is stated explicitly as "the level of perception and attitude," and the output level is left to the literature and the follow-up experiment.

## 6. Links & Deliverables

| Category | Link |
|---|---|
| GitHub Repository | None |
| Deliverables | Performance assessment ① inquiry plan, performance assessment ② research report (11 pages), self-evaluation |
| Related project | [HowTo](HowTo_README.md) (motivation for the inquiry, subject of the follow-up experiment) |

<details>
<summary><b>Terms</b></summary>

- **Mixed methods research**: a design that combines quantitative (questionnaire) and qualitative (interview) methods so each fills the other's gaps
- **Semi-structured interview**: common questions plus follow-up questions tailored to each respondent
- **Mann-Whitney U test**: a nonparametric test that checks the difference between two groups by rank, used when the sample is small or normality is hard to assume
- **Intersubjectivity**: objectivity that includes the perspectives researchers share; reflection on "whose perspective became the standard"
- **Information gap**: differences in digital access and skills turning into yet another form of inequality
- **Critical criminology (conflict theory)**: the view that the criminal justice system can work in favor of the socially powerful
- **Black-box trust** (as defined in this inquiry): trusting an algorithm and considering it objective without knowing how it works
- **Opinions embedded in mathematics** (O'Neil): the idea that formulas that look objective carry the value judgments of their developers and data

</details>

<details>
<summary><b>References</b></summary>

[1] Cathy O'Neil, trans. Kim Jeong-hye, *Weapons of Math Destruction: How Big Data Increases Inequality and Threatens Democracy* (Korean edition), Heureum Publishing, 2017. (Original: C. O'Neil, *Weapons of Math Destruction*, Crown, 2016.)
[2] *High School Society and Culture* textbook (author, publisher and year to be confirmed)
[3] J. Angwin, J. Larson, S. Mattu, and L. Kirchner, "Machine Bias," *ProPublica*, May 23, 2016.
[4] J. Buolamwini and T. Gebru, "Gender Shades: Intersectional Accuracy Disparities in Commercial Gender Classification," *Proceedings of Machine Learning Research*, vol. 81, pp. 77–91, 2018.
[5] J. Dastin, "Amazon scraps secret AI recruiting tool that showed bias against women," *Reuters*, Oct. 10, 2018.
[6] stx4R, "HowTo: An Image Recognition-Based Web Application for Waste Sorting Guidance," GitHub.

</details>

---

+++

## Customer Journey Map

Here the "user" is an ordinary citizen on the receiving end of an algorithm's verdict. Using the interview types and the questionnaire results, I reconstructed how a person comes to accept an algorithm.

| Stage | Action | Thought | What the inquiry found |
|---|---|---|---|
| Contact | Uses recommendation services for 3+ hours a day | "It's convenient and it gets me" | Trust in content recommendation 4.40 |
| Generalization | Extends the experience of recommendations to other domains | "It's a machine, so it must be objective" | "It is objective" 4.13, "I understand it" 2.53 |
| Delegation | Hands over decisions that judge people, too | "Better than a human, at least" | Recidivism prediction at 3.73, higher than hiring, lending or admissions. The partial-trust group's trust by elimination |
| Crack | Comes across a discrimination case | "Must be a temporary glitch" / "That's how the system is built" | The strong-trust group reads it as an error, the strong-distrust group as structural |
| Demand | Wants it corrected | "If it discriminates, it has to be fixed" | "It should be corrected" 3.73, with 6 people giving it a 5 |

## DREAD Risk Assessment

This summarizes the "delegation of high-stakes domains" covered in the inquiry using DREAD. The scores are a qualitative assessment based on the inquiry's results and cases from the literature.

| Threat | D | R | E | A | D | Score | Mitigation (the inquiry's recommendation) |
|---|---|---|---|---|---|---|---|
| Group-dependent misclassification in recidivism prediction (COMPAS) | 3 | 3 | 2 | 3 | 2 | 2.6 | Ensure explainability, mandate final human review |
| Hiring models reproducing past bias (the Amazon case) | 3 | 3 | 2 | 2 | 2 | 2.4 | Check training data representativeness, discrimination impact assessments before and after deployment |
| No verification because of trust without understanding (black-box trust) | 2 | 3 | 3 | 3 | 1 | 2.4 | Algorithm literacy education |

<sub>D·R·E·A·D = Damage · Reproducibility · Exploitability · Affected users · Discoverability (1~3). The score is the average.</sub>
