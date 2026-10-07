# Lesson 01. Introduction and XAI terminology

Lecture notes. Slides are on the course Google Drive.

Lesson outline:
1. Why explain models.
2. Four bugs and "what influenced the output".
3. Language: what exactly we explain.
4. Forms of explanation.
5. Whom to explain to, and where explanations lie.
6. Organizational matters and HW1.

---

## 0. Elective organization

### Grading

$$\text{Final} = 0.2 \cdot \text{Project} + 0.7 \cdot \text{mean}(HW_1, HW_2, HW_3) + 0.1 \cdot \text{Stepik}$$

| HW | Topic | Lessons |
|---|---|---|
| HW1 | Stepik: interpretable models | 01–05 |
| HW2 | Explanations across all modalities | 04, 06–08 |
| HW3 | Verification, attacks, LLMs, agents | 09–12 |

Without a project defense, the final grade is capped at 7–8 out of 10.

### Course blocks

Format: online, 11–12 lessons of 90 minutes each, plus a project defense.

| Block | Lessons | Topic |
|---|---|---|
| I | 01–03 | What it means to explain in ML and DL terms; AI safety; glass boxes |
| II | 04–05 | Post-hoc methods on tabular data |
| III | 06–08 | Beyond tables: images, text, time series, graphs, audio, multimodality |
| IV | 09–10 | How to evaluate explanation quality |
| V | 11–12 | Applied LLM analysis and agents |

Tentative lesson order: what it means to explain → AI safety (jointly with AISF) → glass boxes → PDP, ICE, ALE → LIME, SHAP, counterfactuals → images → text and time series → recsys, graphs, audio → evaluating explanations → attacks on explanations → applied LLM analysis → agents → project defense.

HW release and deadlines:
- HW1: released at lesson 01, due before lesson 05.
- HW2: released at lesson 06, due at lesson 09.
- HW3: released at lesson 10, due by the project defense.

HW1 consists of the Stepik notebooks marked "practice". HW2 and HW3 are released notebooks with points.

### HW1

Course "Interpretable AI Models": [stepik.org/course/228094](https://stepik.org/course/228094).

- Release: at the first lesson.
- Deadline: before the second HW, approximately before lesson 05.
- Grade: fraction of points earned on Stepik, converted to a 10-point scale.

Auto-graded assignments:
- linear models: weights, regularization;
- tutorial: linear regression weights;
- forests and boosting: feature importances;
- SHAP: practice;
- LIME: practice;
- final test.

### Project

A mini-study: take a model and an XAI method and check whether the explanation can be trusted.

1. **Apply.** A model and data, your own or open. Obtain an explanation with a suitable method.
2. **Verify.** Is the explanation correct: sanity check, deletion / insertion, counterfactual.
3. **Validate for fragility.** Different seed, baseline, background, attack. What this means for the user.

Format: a repository with reproducible code, a 4–6 page report, a 10-minute defense.

Timeline: topic by lesson 08, checkpoint by lesson 11, defense at the end of the course (at lesson 12 or asynchronously).

Example topics:
- is SHAP in credit scoring robust to the choice of background;
- does Grad-CAM pass a sanity check on medical images;
- does a probe monitor for an LLM catch the intended behavior.

The project weight is 0.2 of the final grade.

---

## 1. Why explain models

### Where models make decisions about people

Credit limits, anti-fraud, resume screening, feeds and ads, insurance and taxi pricing.

People want different things from an explanation:
- **W** — why the decision is what it is;
- **C** — what to change so that the decision would be different;
- **A** — how to appeal it.

### Domains that require XAI

| Domain | Why |
|---|---|
| Finance | Scoring, limits, anti-fraud. A rejection must be justified to the client and the regulator; the model is audited for discrimination. |
| Medicine | The model supports the physician's decision. The physician must see what it relies on in order to check it. |
| Social sector | Hiring, welfare benefits, justice. An error affects people who lack the resources to contest it. |

### A harmless example: husky or wolf (Ribeiro et al., 2016)

1. **Data:** all wolves are on snow, huskies are without snow.
2. **Model:** high accuracy on a test set from the same distribution.
3. **Explanation:** LIME highlights the snow, not the animal.
4. **Outcome:** people stop trusting the model.

The example is constructed: the classifier was made bad on purpose to test whether people would notice.

### Harmful examples

| Case | Task | What happened |
|---|---|---|
| Amazon, 2018 | Resume screening | The model penalized resumes containing the word "women's". The project was shut down. |
| COMPAS, 2016 | Recidivism risk | Black defendants were falsely labeled "high risk" roughly twice as often (ProPublica). |
| Netherlands, 2021 | Childcare benefits | The risk profile took dual citizenship into account. Thousands of families were falsely accused; the government resigned. |
| Apple Card, 2019 | Credit limits | Complaints about different limits for spouses. The regulator found no discrimination, but the bank could not explain the decisions. |

Medicine:

| Work | Task | What was found |
|---|---|---|
| Zech et al., 2018 | Pneumonia | The model distinguished hospitals from the images, and the prevalence of disease differed across hospitals. Performance dropped at a new hospital. |
| DeGrave et al., 2021 | COVID-19 | Detectors relied on text, markers and patient positioning rather than the lungs. |
| Oakden-Rayner et al., 2020 | Pneumothorax | The model detected the chest drain, i.e. patients already treated. |

### Analogy: Clever Hans

The horse Hans tapped out answers to arithmetic problems with his hoof and was almost always correct. Pfungst (1907) ran a controlled experiment: the questioner does not know the answer, or is not visible to the horse. Accuracy drops to chance. Hans was reading people's posture and facial expressions.

Lesson for ML:
- a correct answer does not imply a correct mechanism;
- verification is an intervention, not test accuracy: remove the suspicious signal and see what happens to the answer.

### Regulation

| Document | What it requires | Implication for the model |
|---|---|---|
| GDPR, Art. 22 (+ Recital 71) | Right not to be subject to a solely automated decision; right to an explanation | The ability to explain a specific decision is needed |
| EU AI Act, Art. 13 and 86 (2024) | Transparency of high-risk systems; right to an explanation of a decision | Documentation and local explanations |
| ECOA / Regulation B (US) | When credit is denied, state the principal reasons | Top rejection reasons for each client |

### Why not just avoid black boxes?

Cynthia Rudin, Duke University. Professor researching interpretable ML; AAAI Squirrel AI Award (2021).

Thesis (2019): in high-stakes tasks on tabular data, an interpretable model is often almost as good as a black box, while a post-hoc explanation is yet another model that may be wrong.

- **For:** tabular data, scoring, medicine; logistic regression, GAM, EBM, shallow trees (lesson 03).
- **Against:** images, text, audio, LLMs; for these there is no interpretable-by-design model of the required quality.
 

### Goals of XAI

| Goal | Content |
|---|---|
| Debugging | Find leakage and shortcuts before production |
| Trust | The user understands when to trust the model |
| Compliance | Justify a decision to the client and the regulator |
| Fairness | Check whether the model uses gender or its proxies |
| Knowledge | Learn what the model has learned that we do not know |

### Everyone needs debugging

Debugging cycle:

1. **The metric is good**, but on part of the data or in production the model behaves strangely.
2. **Explanation:** what the model relies on (SHAP, maps, similar examples).
3. **Hypothesis**, e.g.: "the model looks at the branch, not at income".
4. **Intervention:** remove or permute a feature, shift the data. Does the answer change?
5. **Fix:** data, features, labels, not only hyperparameters.

Four typical bugs:

| Bug | Description |
|---|---|
| Leakage | A feature contains the answer: ID, record time, a "bad" branch |
| Shortcut | Background, a marker on an image, a watermark are associated with the label only in the training data |
| Label noise | Objects with anomalous contributions often turn out to be mislabeled |
| Dataset shift | Importances in production differ from training: the model now relies on something else |

---

## 2. Formalization: what we explain and what we look for

### Whom we ask

$$x \longrightarrow f \longrightarrow \hat y$$

An explanation is built as an approximation of the model:

$$\text{explanation}(f) = g \approx f$$

Hence the explanation has its own error $\lVert g - f \rVert$.

### Bug 1: leakage

$$x_s = g(y,\ \text{collection process}), \qquad x_s \notin \mathcal{I}_t$$

$\mathcal{I}_t$ is the information available at prediction time $t$. The feature $x_s$ carries the label or a trace of the process by which it was recorded.

- **What happens:** the label has leaked into a feature. The feature is computed after the outcome or collected together with the label. At prediction time it will be unavailable, or different.
- **How it shows in the explanation:** a single feature determines the prediction. Suspiciously high importance of a technical feature: ID, record date, branch, data source.
- **Verification by intervention:** rebuild features strictly as of prediction time, separating "training" and "prediction". The honest performance drops.

### Bug 2: shortcut

$$P_{\text{train}}(y \mid s) \neq P_{\text{deploy}}(y \mid s)$$

$s$ is a feature from which the model predicts $y$. The association holds in training and disappears at deployment.

| Task | Intended signal | Shortcut $s$ |
|---|---|---|
| Husky / wolf | muzzle shape, ears | snow in the background |
| Pneumonia | lung opacities | hospital marker |
| Resume screening | experience, skills | the word "women's" |
| Text toxicity | insult | mention of a group of people |

Example from Lapuschkin et al. (2019): a classifier on Pascal VOC assigns an image to the class "horse" based on the source caption (watermark) in the corner. Without the caption the image is not classified as a horse; an artificial image of a car with the same caption is classified as a horse.

### Bug 3: label noise

We observe $\tilde y$, not $y$:

$$P(\tilde y = j \mid y = i) = T_{ij}$$

$T$ is the transition matrix. Noise can be random (uniform) or systematic (certain classes are confused with others).

- **What happens:** labels are noisy. The model reproduces the errors, especially systematic ones: annotator confusion is learned as a rule.
- **How it shows in the explanation:** the explanation contradicts the label. The attribution points to features of another class; some training examples strongly influence erroneous predictions (influence functions).
- **Verification by intervention:** inspect suspicious objects manually, fix the labels, retrain. Does the model's behavior change?

The ImageNet validation set contains at least 6% label errors (Northcutt, Athalye, Mueller, 2021).

### Bug 4: dataset shift

$$P_{\text{train}}(x, y) \neq P_{\text{deploy}}(x, y), \qquad P(x, y) = P(x)\,P(y \mid x)$$

A shortcut is a special case of shift: $P(y \mid s)$ changes for a single feature $s$.

| Type of shift | What changes | What stays | Example |
|---|---|---|---|
| Covariate shift | $P(x)$ | $P(y \mid x)$ | Different clients arrive, the rule "income → risk" is the same. The model may err where training data is scarce. |
| Label shift | $P(y)$ | $P(x \mid y)$ | The default rate rose during a crisis; model calibration breaks. |
| Concept shift | $P(y \mid x)$ | — | The same features now mean something else. The explanation describes the old world. |

Role of XAI: compare attributions in training and in production. If importances have shifted significantly, look for what changed.

### Exercise: find the shortcut

| Situation | Setup | Answer | Check |
|---|---|---|---|
| A. Customer churn model | The most important feature is "date of last call to support" | Leakage from the future: the client calls to terminate the contract, the call is a consequence of churn | Features only as of prediction time |
| B. Toxic comment detector | "I am non-binary" gets a high toxicity score, "I am straight" a low one | In the training data, group mentions occurred more often in insults (Dixon et al., 2018) | Templates "I am <group>" with substitution |
| C. Tank classifier | Own and enemy tanks were distinguished perfectly; in the field the model did not work | Poor train / test split | — |

### Necessity and sufficiency

The question "what influenced the answer" splits into two.

| | Necessity | Sufficiency |
|---|---|---|
| Meaning | Without $x$ there would be no $y$ | With $x$ there would be $y$ |
| Logic | $\neg x \Rightarrow \neg y$ | $x \Rightarrow y$ |
| Probability | $\text{PN} = P(Y_{x'} = y' \mid X = x, Y = y)$ | $\text{PS} = P(Y_{x} = y \mid X = x', Y = y')$ |
| Example | Pfungst removed the audience's cue, and Hans's "counting" broke: the cue was necessary | Snow in the background is enough for the model to say "wolf" even without a wolf |

$Y_{x'}$ is the outcome that would have occurred without $x$ (a counterfactual). Necessity and sufficiency jointly:

$$\text{PNS} = P(Y_x = y,\ Y_{x'} = y')$$

Source of the definitions: Pearl (1999). We will not decompose every method in the course this way, but this framework is useful to keep in mind.

---

## 3. How one can explain: terms

### Interpretability and explanation

- **Interpretability** (Doshi-Velez & Kim, 2017) — the ability to explain or present the model's behavior in human-understandable terms.
- **Explanation** (Miller, 2019) — an answer to the question "why?". People ask contrastively: why P rather than Q.

In this course:
- an interpretable model is understandable as a whole (weights, tree);
- an explanation is a separate artifact describing the model's behavior on an object or overall.

### Model, object, explanation

$$f : \mathcal{X} \to \mathcal{Y}, \qquad x \in \mathbb{R}^d, \qquad \hat y = f(x)$$

$$E(f, x) \to \text{contributions} \mid \text{rule} \mid \text{example} \mid \text{counterfactual}$$

| Type | Question | Notation |
|---|---|---|
| Local | About a single object $x$: why was this client rejected | $E(f, x)$ |
| Global | About the model on the data distribution: how does income affect risk overall | $E(f, P(X))$ |

### Local and global differ

$$\mathbb{E}_x\,\lvert \varphi_j(x) \rvert \quad \text{vs} \quad \varphi_j(x_0)$$

Example with the feature "delinquencies": most clients have no delinquencies, so the feature's mean contribution is small. For a client with two delinquencies the contribution is +0.87, the second largest.

Ranking by mean importance answers a question about the model. A client's complaint is a question about an object. These are different explanations.

### Taxonomy axes

| Axis | Option A | Option B | Examples in the course |
|---|---|---|---|
| Model | intrinsic: the model is explainable by itself | post-hoc: we explain a trained model | weights, EBM, tree · SHAP, LIME |
| Model access | model-specific: internals are needed | model-agnostic: only queries $f(x)$ | Grad-CAM, TreeSHAP · LIME, occlusion |
| Form | On the same object: feature coefficients, heatmaps | Outside the object: rules, counterfactuals, another model | section 4 |

### Exercise: place the methods on the axes

| Method | intrinsic / post-hoc | local / global | specific / agnostic |
|---|---|---|---|
| Logistic regression weights | intrinsic | both: weight and weight × feature | specific |
| Heatmap (any) | post-hoc | local | specific |
| Counterfactual | post-hoc | local | agnostic or gradient-based |
| Transformer attention | part of the model | local | specific (with caution) |

---

## 4. Forms of explanation

### The explanation is the model itself

Logistic regression:

$$\log \frac{P(y = 1)}{P(y = 0)} = \beta_0 + \sum_j \beta_j x_j$$

$\beta_j$ is the change in log-odds when $x_j$ increases by one standard deviation, all else being equal.

Example: income weight $-0.76$, $e^{-0.76} \approx 0.47$. An increase in income by $1\sigma$ roughly halves the odds of default.

### The explanation is a root-to-leaf path

A rule from a decision tree:

$$\text{IF debt load} > 0.35 \ \text{AND income} \le 78 \ \text{THEN} \ P(\text{default}) = 0.57$$

### The explanation is a smaller model (LIME)

$$g^* = \arg\min_{g \in G} \ L(f, g, \pi_x) + \Omega(g)$$

- $\pi_x$ — proximity weight of a point to $x$;
- $L$ — discrepancy between $g$ and $f$ in the neighborhood of $x$;
- $\Omega(g)$ — complexity penalty on $g$.

Near $x$, the surrogate's boundary approximates the model's true boundary well.

### The explanation is a region of the data (Grad-CAM)

$$L^c = \mathrm{ReLU}\Big(\sum_k \alpha_k^c A^k\Big), \qquad \alpha_k^c = \operatorname{mean}_{h,w} \frac{\partial f^c}{\partial A^k_{hw}}$$

- $A^k$ — activations of channel $k$;
- $\alpha_k^c$ — importance of channel $k$ for class $c$.

At the last layer the map is 7×7 and object-level. At earlier layers the map is more detailed but noisier.

### Four forms of answer

| Form | Question | Notation |
|---|---|---|
| Importances | Which features shifted the prediction | $\varphi_j(x),\ j = 1, \dots, d$ |
| Examples | Which training objects this one resembles | $x^{(i)} \in \text{train}$, close to $x$ |
| Rules | Under what condition the answer is preserved | IF … THEN $\hat y$, precision $\ge \tau$ |
| Counterfactuals | What minimal change makes the answer different | $x' = \arg\min d(x, x') : f(x') \ne f(x)$ |

### Counterfactual

$$x' = \arg\min_{x'} d(x, x') \quad \text{s.t.} \quad f(x') \ne f(x)$$

Example for a rejected client:
- $x'_1$: income 40 → 69 thousand, debt load 0.58 → 0.47;
- $x'_2$: only income 40 → 84 thousand.

Requirements for a counterfactual:
- **minimal:** few changes;
- **actionable:** only mutable features change (age does not change);
- **plausible:** such objects occur in the data.

### Examples and rules

- **Examples** (kNN, ProtoPNet — Chen et al., 2019). "This looks like that": the model compares the object with learned prototypes or neighbors from the training set.
- **Rules** (Anchors — Ribeiro et al., 2018). For example: IF debt load > 0.5 AND delinquencies ≥ 2 THEN reject.
  - precision — how often the rule yields the same answer;
  - coverage — on what fraction of the data the rule holds.

---

## 5. Whom to explain to

### Different people ask different questions

| Who | What they ask | What they need | Form |
|---|---|---|---|
| Developer | Where does the model err and why? | Global picture, leakage search | importances, PDP, maps |
| User | Why the rejection and what should I do? | A clear reason and an action | counterfactual |
| Expert (physician) | Can it be trusted in this case? | Reliance on familiar features | examples, maps |
| Regulator | Does the model discriminate? | Auditability, reproducibility | global explanations and audit |
| Affected person | Can the decision be contested? | A specific reason and an appeal path | counterfactual and rule |

### How people ask "why" (Miller, 2019)

| Property | Content | Notation |
|---|---|---|
| Contrastive | "Why rejection rather than approval?", not "why rejection at all" | why P, not Q? |
| Selective | People need 1–3 reasons, not 30 contributions | top-k ≪ d |
| Social | The explanation is tailored to the listener and their knowledge | $E = E(f, x, \text{who})$ |

A vector of contributions over 50 features does not match what a person calls an explanation.

### Properties of an explanation

| Property | Question | How to measure |
|---|---|---|
| Faithfulness | Does the explanation reflect the actual model | $f(x) - f(x \text{ without top-}k\text{ important})$ |
| Stability | Similar objects receive similar explanations | $\lVert \varphi(x) - \varphi(x + \varepsilon) \rVert$ |
| Comprehensibility | A person can use the explanation within a minute | subjective metric |
| Actionability | One can act: change income, not age | $x' \in$ feasible set (subjective) |

Plausibility ("looks reasonable") is not the same as faithfulness ("true about the model").

---

## Next

- Lesson 02 (Thursday): AI safety — what can go wrong with powerful AI.
- On Saturday we continue the formalization.
- Before the HW1 deadline, work through the Stepik course.

---

## Sources from the slides

- Ribeiro, Singh, Guestrin. "Why should I trust you?": Explaining the predictions of any classifier. KDD, 2016.
- Zech et al. Variable generalization performance of a deep learning model to detect pneumonia in chest radiographs: a cross-sectional study. PLOS Medicine, 2018.
- DeGrave, Janizek, Lee. AI for radiographic COVID-19 detection selects shortcuts over signal. Nature Machine Intelligence, 2021.
- Oakden-Rayner, Dunnmon, Carneiro, Ré. Hidden stratification causes clinically meaningful failures in machine learning for medical imaging. ACM CHIL, 2020.
- Rudin. Stop explaining black box machine learning models for high stakes decisions and use interpretable models instead. Nature Machine Intelligence 1(5), 206–215, 2019.
- Lapuschkin, Wäldchen, Binder, Montavon, Samek, Müller. Unmasking Clever Hans predictors and assessing what machines really learn. Nature Communications, 2019. [arXiv:1902.10178](https://arxiv.org/abs/1902.10178)
- Northcutt, Athalye, Mueller. Pervasive label errors in test sets destabilize machine learning benchmarks. NeurIPS Datasets and Benchmarks, 2021.
- Northcutt, Jiang, Chuang. Confident learning: estimating uncertainty in dataset labels. JAIR 70, 2021.
- Koh, Liang. Understanding black-box predictions via influence functions. ICML, 2017.
- Quiñonero-Candela, Sugiyama, Schwaighofer, Lawrence (eds.). Dataset Shift in Machine Learning. MIT Press, 2009.
- Dixon, Li, Sorensen, Thain, Vasserman. Measuring and mitigating unintended bias in text classification. AIES, 2018.
- Pearl. Probabilities of causation: three counterfactual interpretations and their identification. Synthese 121, 1999.
- Doshi-Velez, Kim. Towards a rigorous science of interpretable machine learning. arXiv:1702.08608, 2017.
- Miller. Explanation in artificial intelligence: insights from the social sciences. Artificial Intelligence 267, 1–38, 2019.
- Chen, Li, Tao, Barnett, Su, Rudin. This looks like that: deep learning for interpretable image recognition. NeurIPS, 2019.
- Ribeiro, Singh, Guestrin. Anchors: high-precision model-agnostic explanations. AAAI, 2018.
