<div align="center">

# 🧠 CTB — A Cognitive Battery for Foundation Models

**Five theory-grounded benchmarks. Procedurally generated. Profile-based scoring.**

[![License: MIT](https://img.shields.io/badge/code-MIT-blue.svg)](LICENSE)
[![Data: CC BY 4.0](https://img.shields.io/badge/data-CC%20BY%204.0-lightgrey.svg)](LICENSE-DATA)
[![Paper](https://img.shields.io/badge/paper-ICML%202026%20workshop-b31b1b.svg)](paper/paper.pdf)
[![Items](https://img.shields.io/badge/items-25%2C390-green.svg)](#-the-five-benchmarks)

*Attention · Learning · Metacognition · Executive Function · Social Cognition*

</div>

---

## Why another benchmark?

Most benchmarks report a single accuracy number on a fixed item pool —
opaque to overfit, silent about *why* a model fails, and disconnected
from the cognitive constructs they nominally measure.

**CTB takes a different cut.** Each of its five benchmarks:

- 🎯 **Operationalises a construct** drawn from cognitive science, not a task scraped from the web.
- 📈 **Traces a degradation profile** along a controlled difficulty axis, not a point estimate.
- 🛡️ **Ships with anti-heuristic controls** (true-belief twins, irregular-form traps, same-number lures, prepotent overrides).
- 🔁 **Is procedurally generated** — no test set to leak, easy to refresh, parameterised end-to-end.

The companion paper, [`paper/paper.pdf`](paper/paper.pdf), explains
how the resulting profiles plug into the theory–benchmark virtuous
cycle: a candidate theory of a capability becomes a *shape prediction*
over the profile, which a single accuracy number could never falsify.

---

## 🧪 The five benchmarks

| Code  | Benchmark                                  | Construct           | Difficulty axis         |  Items  | Sub-tasks | Score |
|:-----:|--------------------------------------------|---------------------|-------------------------|--------:|----------:|:-----:|
| **CDI**   | Controlled Distractor Injection            | Selective attention | Distractor count (0–6)         | 4,938  | 229 | bool  |
| **ALGIn** | Alien Grammar Induction                    | Fluid learning      | Examples in context (2/4/8/12) | 5,585  | 223 | bool  |
| **ECUU**  | Epistemic Calibration Under Uncertainty    | Metacognition       | Unknowability tier (1–10)      | 4,975  | 226 | float |
| **DRO**   | Dynamic Rule Override                      | Executive function  | Rule / prepotency depth (1–6)  | 4,976  | 230 | bool  |
| **RBT**   | Recursive Belief Tracking                  | Theory of mind      | Recursion order (1–4)          | 4,916  | 230 | bool  |
|       |                                            |                     | **Total**              | **25,390** | **1,138** |       |

<details>
<summary><b>CDI — Controlled Distractor Injection</b> · selective attention</summary>

Holds a core task constant while parametrically injecting 0–6 irrelevant
distractor paragraphs at controlled positions (before, after, buried,
surrounded). An adversarial condition uses same-number lure distractors
to defeat lexical heuristics. Draws on filter theory, the cocktail-party
effect, inattentional blindness, and the *Lost in the Middle* phenomenon.
</details>

<details>
<summary><b>ALGIn — Alien Grammar Induction</b> · in-context fluid learning</summary>

Procedurally generated "alien" languages with novel phonological
inventories (4–8 consonants, 3–5 vowels), morphological rules
(affixation, reduplication, vowel shift, infixation, circumfixation),
and a word order drawn from six typologically attested options. The
model sees *k* ∈ {2, 4, 8, 12} glossed examples and is asked to produce
or judge new forms. Irregular-form families trap rule overgeneralisation.
</details>

<details>
<summary><b>ECUU — Epistemic Calibration Under Uncertainty</b> · metacognition</summary>

Asks models to commit to an answer **and** a confidence in the same
turn, scored with the inverted Brier score (a proper scoring rule).
Ten difficulty tiers run from trivially solvable to *provably unknowable*.
Multi-turn protocols adapt Hart's feeling-of-knowing, Fischhoff's
hindsight bias, the Rozenblit–Keil illusion of explanatory depth, and
boundary awareness.
</details>

<details>
<summary><b>DRO — Dynamic Rule Override</b> · executive function</summary>

Classical executive-function paradigms: perseveration (WCST), inhibitory
control (Stroop, Go/No-Go), planning (Tower of Hanoi, grid pathfinding),
working memory (N-back, digit span, running memory), and feedback-based
learning (Iowa Gambling Task). A **prepotent completion suppression**
family tests whether a model can override its default next-token
continuation:

> *"Roses are red, violets are ___. Do NOT complete the poem.
> Instead, output the single word 'green'."*
</details>

<details>
<summary><b>RBT — Recursive Belief Tracking</b> · theory of mind</summary>

Procedurally generated false-belief scenarios at recursion orders 1–4,
with the three classes of anti-heuristic controls from Ullman (2023):
true-belief controls, double-move items, indirect access. Additional
families cover pragmatic inference, faux-pas detection, emotional ToM,
deception detection, and perspective taking. Designed to test the
*ToM cliff* hypothesis at the order-3 boundary.
</details>

---

## 📂 Repository layout

```
ctb-bench/
├── datasets/      # v0.1 CSV snapshots (one per benchmark)
├── tasks/         # Per-benchmark scoring definitions (kbench JSON)
├── generators/    # Source notebooks that produce the full battery
└── paper/         # ICML 2026 workshop paper (camera-ready PDF)
```

---

## ⚡ Quick start

Inspect the released snapshot in a few lines of pandas:

```python
import pandas as pd

df = pd.read_csv("datasets/rbt_dataset.csv")
print(df.columns.tolist())
print(df.groupby(["type", "dlevel"]).size().head())

# A single item
row = df.iloc[0]
print(row["prompt"][:400], "→", row["answer"])
```

Score model responses against a benchmark's gold scoring function:

```python
import json
spec = json.load(open("tasks/recursive_belief_tracking.task.json"))
print(spec["name"], "·", spec["description"])
# spec["definition"] is the Python @kbench.task scoring function
```

---

## 📊 Data format

### CSV snapshots — `datasets/*.csv`

Static **v0.1 sample snapshots** for quick inspection and small-scale
evaluation. They do **not** contain every paradigm family at every
difficulty; the full 25,390-item battery in the paper is reproduced
by running the generator notebooks.

| File                | Rows (excl. header) |
|:--------------------|--------------------:|
| `algin_dataset.csv` | 11,445 |
| `cdi_dataset.csv`   | 3,889 |
| `dro_dataset.csv`   | 1,508 |
| `ecuu_dataset.csv`  | 330 |
| `rbt_dataset.csv`   | 1,055 |

Columns include `prompt`, `answer`, `type` (paradigm family),
`dlevel` (difficulty), plus benchmark-specific fields.

### Task definitions — `tasks/*.task.json`

kbench-format scoring functions. The `definition` field carries a
Python `@kbench.task` that decides whether a model response matches
the gold answer for each `type`. This is the **authoritative scoring
rule** — CSVs are samples, this is the judge.

### Generators — `generators/task_*.py`

The source notebooks that produced the full battery: distractor
library, rule templates, alien-language grammar engine, item
construction.

> **Note on running the generators.** They import `kaggle_benchmarks`
> (`kbench`), the runner from the Kaggle *Measuring Progress Toward
> AGI* competition. `kbench` is not yet open-sourced; the generators
> are included primarily as **canonical specifications** of how each
> item is constructed. To run them end-to-end, you'll need either the
> `kbench` package or to stub the `@kbench.task` decorator and the
> `kbench.assertions.*` helpers. A reference reimplementation is on
> the v0.2 roadmap.

---

## 🚧 Status & roadmap

- **v0.1 (now)** — design, items, scoring, paper.
- **v0.2 (planned)** — model evaluation results, human baseline pilot
  data, cross-task factor analysis, open-source `kbench` reference.

Contributions, bug reports, and ports of the scoring functions to other
runners are very welcome — open an issue or PR.

---

## 📝 Citation

```bibtex
@inproceedings{ctb2026,
  title     = {A Cognitive Battery for Foundation Models:
               Theory-Grounded Benchmarks for Attention, Learning,
               Metacognition, Executive Function, and Social Cognition},
  author    = {Zacharie B.},
  booktitle = {ICML 2026 Workshop on Combining Theory and Benchmarks},
  year      = {2026},
  note      = {Camera-ready, poster track}
}
```

## 📄 License

- **Code** (`generators/`, `tasks/`) — MIT, see [LICENSE](LICENSE)
- **Data** (`datasets/`) — Creative Commons Attribution 4.0,
  see [LICENSE-DATA](LICENSE-DATA)
