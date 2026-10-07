# Explainable Artificial Intelligence (XAI) — elective, 2026/27

**The question of the course:** "Why did the model make this decision — and can we trust the answer?"
The first half teaches how to obtain explanations; the second half teaches how to check them and apply them where we all are today: LLMs and agents.

Instructor: Sabrina Sadiekh.

## How to use this repository

- Materials are added as the course goes. Update your copy before each lesson: `git pull`.
- Each lesson folder contains a README with the topics, short lecture notes and references and, where available, a demo notebook with the code of all examples.
- Slides are posted in the course chat.
- Assignments are in `homeworks/`: a notebook and a README with the task, points and deadlines.
- Reference solutions may be published later.

## Lessons: 12 sessions + project defence

| # | Block | Lesson | HW |
|---|---|---|---|
| 01 | I. What does it mean to "explain" | [Introduction: why XAI, taxonomy of methods](lessons/01_intro/) | HW1 released (Stepik) |
| 02 | I | [AI safety: risks, alignment, defense in depth](lessons/02_ai_safety_alignment/) | |
| 03 | I | [Glass boxes: linear models, GAM/EBM, trees and boosting](lessons/03_glass_boxes/) | |
| 04 | II. Post-hoc on tabular data | Global model-agnostic methods: permutation importance, PDP, ICE, ALE | |
| 05 | II | Local methods: LIME, SHAP, counterfactuals | HW1 deadline — 2 weeks later |
| 06 | III. Beyond tables | Images: gradients, Grad-CAM, IG, RISE | HW2 released |
| 07 | III | Text and time series | |
| 08 | III | Recommender systems, graphs, audio, multimodal models | |
| 09 | IV. Can we trust it? | Evaluating explanations: faithfulness, sanity checks, necessity and sufficiency | HW2 deadline |
| 10 | IV | Fragility of and attacks on explanations | HW3 released |
| 11 | V. Where this leads | Applied analysis of LLMs | |
| 12 | V | Explaining agents + project kick-off | |
| — | | Mini-research project defence | HW3 and all Stepik deadline |

## Homework

| HW | Topic | Covers | Format |
|---|---|---|---|
| [HW1](homeworks/hw1_stepik/) | Interpretable models and post-hoc methods on tabular data | lessons 01, 03–05 | four practice blocks of the Stepik course ["Interpretable AI models"](https://stepik.org/course/228094), auto-graded |
| [HW2](homeworks/hw2_modalities/) | Explanations across all modalities | lessons 04, 06–08 | notebook: tables, images, text, time series, audio, graphs, recommendations, multimodal |
| [HW3](homeworks/hw3_trust_llm_agents/) | Can we trust an explanation: evaluation, attacks, LLMs, agents | lessons 09–12 | notebook: sanity checks and faithfulness, an attack on SHAP, context attribution in an LLM, necessity in agent logs |

Deadlines, points and submission rules will be given in the README of each assignment.

## Grading

```
Final = 0.2·Project + 0.7·MEAN(HW1, HW2, HW3) + 0.1·Stepik
```

- **HW1** — four practice blocks on Stepik ("…: practice"); deadline 2 weeks after lesson 05.
- **Stepik** — all other quiz tasks of the course; deadline — end of the course.
- Stepik IDs are collected one week before the HW1 deadline. Stepik results are exported twice: after the HW1 deadline and at the end of the course. Details are in the [HW1 README](homeworks/hw1_stepik/).

Without the project defence the final grade is at most 7–8 out of 10. The project is a mini-research study: apply an XAI method to your own task and check the explanation and its robustness.

## Environment

Python 3.12. Installation with [uv](https://docs.astral.sh/uv/):

```bash
git clone <repository link> && cd xai_fac_2026_2027
uv venv --python 3.12 && uv pip install -r requirements.txt
source .venv/bin/activate
python -m ipykernel install --user --name xai_fac   # Jupyter kernel
```

Without uv: `python3.12 -m venv .venv && source .venv/bin/activate && pip install -r requirements.txt`.

Everything except part 3 of HW3 (a 0.5B-parameter LLM, ~1 GB) runs on a laptop CPU. A GPU (CUDA or Apple MPS) is not required — we live frugally! Models and data are downloaded on the first run of the notebooks.

## Structure

```
lessons/NN_*/        lesson README (topics and notes), demo notebook
homeworks/hwN_*/     assignment notebook and README (+ data/ for HW3)
requirements.txt     library versions on which all notebooks were tested
```
