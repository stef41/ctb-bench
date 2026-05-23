# CTB — A Cognitive Battery for Foundation Models

Five procedurally generated benchmarks for foundation models, each
operationalising a widely studied family of cognitive constructs:

| Code  | Benchmark                                  | Construct           | Items  | Sub-task types | Scoring |
|-------|--------------------------------------------|---------------------|-------:|---------------:|---------|
| CDI   | Controlled Distractor Injection            | Selective attention | 4,938  | 229            | bool    |
| ALGIn | Alien Grammar Induction                    | Fluid learning      | 5,585  | 223            | bool    |
| ECUU  | Epistemic Calibration Under Uncertainty    | Metacognition       | 4,975  | 226            | float   |
| DRO   | Dynamic Rule Override                      | Executive function  | 4,976  | 230            | bool    |
| RBT   | Recursive Belief Tracking                  | Theory of mind      | 4,916  | 230            | bool    |
| **Total** |                                        |                     | **25,390** | **1,138**   |         |

Rather than report a single accuracy number, each benchmark traces a
**degradation profile** along a controlled difficulty axis (distractor
count, rule count, recursion order, prepotent strength, unknowability
tier), so the output is a parametric probe whose shape a candidate
theory of the underlying capability can commit to a prediction about.

See the companion paper, [`paper/paper.pdf`](paper/paper.pdf), for
the design rationale, anti-heuristic controls, and how the resulting
profiles fit into the theory–benchmark virtuous cycle.

## Repository layout

```
ctb-bench/
├── datasets/          # CSV releases (v0.1) — one per benchmark
│   ├── algin_dataset.csv
│   ├── cdi_dataset.csv
│   ├── dro_dataset.csv
│   ├── ecuu_dataset.csv
│   └── rbt_dataset.csv
├── tasks/             # Per-benchmark task definitions (JSON, kbench-format)
│   ├── alien_grammar_induction.task.json
│   ├── controlled_distractor_injection.task.json
│   ├── dynamic_rule_override.task.json
│   ├── epistemic_calibration.task.json
│   └── recursive_belief_tracking.task.json
├── generators/        # Generator notebooks (Python, one per benchmark)
│   ├── task_attention_cdi.py
│   ├── task_executive_dro.py
│   ├── task_learning_algin.py
│   ├── task_metacognition_ecuu.py
│   └── task_social_rbt.py
└── paper/
    └── paper.pdf      # ICML 2026 workshop paper (camera-ready)
```

## The five benchmarks

### CDI — Controlled Distractor Injection (Attention)

Holds a core task constant while parametrically injecting 0–6
irrelevant distractor paragraphs at controlled positions (before,
after, buried, surrounded). Includes an adversarial condition with
same-number lure distractors to defeat lexical heuristics. Draws on
filter theory, the cocktail-party effect, inattentional blindness,
and the "Lost in the Middle" phenomenon. Difficulty axis: distractor
count.

### ALGIn — Alien Grammar Induction (Learning)

In-context fluid learning via procedurally generated "alien"
languages. Each language has a novel phonological inventory (4–8
consonants, 3–5 vowels), morphological rules (affixation,
reduplication, vowel shift, infixation, circumfixation), and a word
order drawn from six typologically attested options. The model sees
*k* ∈ {2, 4, 8, 12} glossed examples and is asked to produce or
judge new forms. Adversarial families include irregular-form items
that trap rule overgeneralisation.

### ECUU — Epistemic Calibration Under Uncertainty (Metacognition)

Asks models to commit to an answer **and** a confidence in the same
turn. Calibration items are scored with the inverted Brier score (a
proper scoring rule). Ten difficulty categories range from trivially
solvable to provably unknowable. Multi-turn protocols adapt Hart's
feeling-of-knowing, Fischhoff's hindsight bias, the Rozenblit–Keil
illusion of explanatory depth, and boundary awareness.

### DRO — Dynamic Rule Override (Executive Function)

Neuropsychological executive-function paradigms: perseveration
(WCST), inhibitory control (Stroop, Go/No-Go), planning (Tower of
Hanoi, grid pathfinding), working memory (N-back, digit span,
running memory), and feedback-based learning (Iowa Gambling Task).
Includes a **prepotent completion suppression** family that tests
whether a model can override its default next-token continuation
("Roses are red, violets are ___. Do NOT complete the poem. Instead,
output the single word 'green'."). Difficulty 1–6.

### RBT — Recursive Belief Tracking (Social Cognition)

Procedurally generated false-belief scenarios at recursion orders
1–4, with the three classes of anti-heuristic controls from Ullman
(2023): true-belief controls, double-move items, indirect access.
Additional families cover pragmatic inference, faux-pas detection,
emotional ToM, deception detection, and perspective taking. Designed
to test the "ToM cliff" hypothesis at the order-3 boundary.

## Format

**CSV datasets** (`datasets/*.csv`) are static **v0.1 sample snapshots**
for quick inspection and small-scale evaluation (header + rows shown
below). They do not contain every paradigm family at every difficulty;
the full 25,390-item battery described in the paper is reproduced by
running the generator notebooks.

| File | Rows (excl. header) |
|------|--------------------:|
| `algin_dataset.csv` | 11,445 |
| `cdi_dataset.csv`   | 3,889 |
| `dro_dataset.csv`   | 1,508 |
| `ecuu_dataset.csv`  | 330 |
| `rbt_dataset.csv`   | 1,055 |

Columns include `prompt`, `answer`, `type` (paradigm family),
`dlevel` (difficulty), and benchmark-specific fields.

**Task definitions** (`tasks/*.task.json`) are kbench-format scoring
functions: a `definition` field containing the Python `@kbench.task`
that decides whether a model response matches the gold answer for
each `type`. They are the authoritative scoring rule.

**Generators** (`generators/task_*.py`) are the source notebooks
that produced the full battery. They contain the distractor library,
rule templates, alien-language grammar engine, and item-construction
logic.

### Running the generators

The generators import `kaggle_benchmarks` (`kbench`), the runner
used for the Kaggle *Measuring Progress Toward AGI* competition.
`kbench` is not currently open-sourced; the generators are included
here primarily as **canonical specifications** of how each item is
constructed. To run them end-to-end, you will need either the
`kbench` package or to stub the `@kbench.task` decorator and the
`kbench.assertions.*` helpers. A reference re-implementation is
on the roadmap for v0.2.

## Citation

If you use this battery, please cite the paper:

```bibtex
@inproceedings{ctb2026,
  title  = {A Cognitive Battery for Foundation Models:
            Theory-Grounded Benchmarks for Attention, Learning,
            Metacognition, Executive Function, and Social Cognition},
  author = {Zacharie B.},
  booktitle = {ICML 2026 Workshop on Combining Theory and Benchmarks},
  year   = {2026},
  note   = {Camera-ready, poster track}
}
```

## License

- Code (`generators/`, `tasks/`) — MIT (see [LICENSE](LICENSE))
- Data (`datasets/`) — Creative Commons Attribution 4.0
  (see [LICENSE-DATA](LICENSE-DATA))

## Status

This is the v0.1 release: design, items, and scoring. Model
evaluation results, human baseline pilot data, and the cross-task
factor analysis are deferred to v0.2.
