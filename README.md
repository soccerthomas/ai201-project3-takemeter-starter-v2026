# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

TakeMeter is a discourse-quality classifier built using public comments from r/soccer during the 2026 World Cup. It separates comments into three categories: emotional reactions, unsupported hot takes, and evidence-backed analysis.

The goal is to identify meaningful soccer discussions rather than treating every comment as equally informative. I used 200 labeled comments and fine-tuned a transformer model to recognize these distinctions.

---

## Label Taxonomy

### `reaction`

**Definition:** An emotional or immediate response without a substantive claim about the game.

**Example 1:** "NOOOO not like this again 😭😭"

**Example 2:** "I have never been so stressed in my life lmaooo"

### `hot_take`

**Definition:** A debatable judgment or claim with little or no supporting reasoning.

**Example 1:** "Mbappé is honestly overrated, France play better without him."

**Example 2:** "This is the worst refereeing performance of the entire tournament, embarrassing."

### `analysis`

**Definition:** A claim supported by reasoning, evidence, or a specific and checkable observation about play.

**Example 1:** "Brazil's midfield got overrun because they pushed both 8s forward and left Casemiro isolated — every transition came straight through that gap."

**Example 2:** "The penalty was soft but the bigger issue was the defending before it: the fullback stepped up with no cover."

### The Hardest Boundary

The hardest boundary was `hot_take` versus `analysis`.

**Decision rule:** A comment qualifies as analysis only when its reasoning identifies something specific and checkable, such as a statistic, tactical pattern, or match event. Vague claims and unsupported judgments remain hot takes, even when they sound analytical.

For `reaction` versus `hot_take`, I used a second rule: if an emotional statement contains an identifiable debatable claim, it is a hot take. Otherwise, it is a reaction.

---

## The Dataset

**Source:** Public r/soccer comments from 2026 World Cup discussions, including daily discussion, banter, and post-match threads.

**Collection and labeling:** I recovered the 200-comment dataset from my original TakeMeter repository. The original workflow involved manually collecting public comments, using AI for pre-labeling assistance, and reviewing and correcting the labels. Borderline decisions were documented using the taxonomy rules.

For this version, I reused the original dataset, renamed the CSV column `notes` to `note`, and verified the label distribution and duplicate count.

**Counts per label:**

| Label | Count | Share |
|---|---:|---:|
| hot_take | 70 | 35% |
| analysis | 66 | 33% |
| reaction | 64 | 32% |
| **Total** | **200** | **100%** |

There were zero duplicate texts.

### Three Hard Cases

**1.** "Brazil need a midfield. Their transitioning was so poor."

- Could have been: `hot_take` or `analysis`
- Decision: `hot_take`
- Reason: It identifies a weakness but does not explain a specific mechanism or provide supporting evidence.

**2.** "Notable teams England have beaten since Euro 2016: [list]. Scotland: Haiti."

- Could have been: `hot_take` or `analysis`
- Decision: `hot_take`
- Reason: The list is used primarily for banter rather than to support a substantive argument about play.

**3.** "They have a striker who had 30 g/a last season and won POTS at a PL club..."

- Could have been: `hot_take` or `analysis`
- Decision: `analysis`
- Reason: The claim is supported by specific, checkable statistics and an achievement.

---

## The Training Run

**Base model:** `distilbert-base-uncased`, fine-tuned for three-class text classification.

**Dataset split:** 140 training, 30 validation, and 30 testing examples (70/15/15). The held-out test set contains 10 examples from each label.

**Training configuration:**

- Random seed: 42
- Maximum sequence length: 128 tokens
- Notebook default epochs: 3
- Notebook default learning rate: 2e-5
- Notebook default batch size: 16

**Hardware:** NVIDIA GeForce RTX 4050 Laptop GPU using CUDA and PyTorch 2.14.1+cu130.

The current notebook produced `results.json` and `test_split.csv`.

### Results

| Metric | Result |
|---|---:|
| Overall accuracy | 0.600 |
| Macro-F1 | 0.583 |
| Analysis F1 | 0.750 |
| Hot take F1 | 0.500 |
| Reaction F1 | 0.500 |
| Analysis recall | 0.900 |

The classifier correctly identified 9 of 10 analysis comments. It struggled more with reactions, correctly identifying 4 of 10.

These are results from the new notebook run, not copied from the previous project evaluation.

The training settings above describe the notebook defaults and should be checked against the executed training cell before submission.

---

## How I Used AI

### Moment 1 — Label Taxonomy and Annotation

**What I asked:** In the original project, I used Claude to help examine difficult distinctions between reactions, unsupported claims, and evidence-backed analysis.

**What came back:** Suggestions for resolving ambiguous comments, especially those that mentioned tactics without providing specific supporting evidence.

**What I changed:** I applied a consistent rule requiring specific, checkable support before assigning the analysis label. The original project records that I reviewed and corrected the pre-labeled comments.

### Moment 2 — Recovering and Updating the Project

**What I asked:** I used ChatGPT to help recover my previous TakeMeter work and adapt it to the current CodePath starter repository.

**What came back:** Instructions for recovering the original CSV, correcting its column names, matching the label definitions, setting up Python 3.13, and running the training notebook.

**What I changed:** I restored the original 200 comments, renamed `notes` to `note`, updated the label definitions and acceptance criteria, and executed the notebook locally.

**Pre-labeling disclosure:** AI-assisted pre-labeling was used in the original project. The original project documents human review and corrections. The dataset was reused for this version rather than recollected.

**Reuse disclosure:** This submission builds on my previous TakeMeter project. The dataset, taxonomy, and original annotation decisions were reused, while the current notebook generated new training results.

### Moment 3 — Unit 6 testing

I used Claude to walk through the commands and format the results tables. Summing the confusion matrix across seeds showed the reaction-to-hot_take confusion runs one way (17 vs 3), and I labeled the staff posts myself.

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.567 | 0.600 | +0.033 |
| Macro F1 | 0.553 | 0.583 | +0.031 |
| F1 — `analysis` | 0.636 | 0.750 | +0.114 |
| F1 — `hot_take` | 0.560 | 0.500 | -0.060 |
| F1 — `reaction` | 0.462 | 0.500 | +0.038 |

**What I predicted before I looked:**

I predict the trained model will beat the baseline overall, and by the most on `analysis`, because the baseline has to guess what counts as specific, checkable support, while the trained model learned that boundary from my 66 labeled analysis examples. *(Written after the bare-names baseline run, before seeing the trained numbers.)*

**How it turned out:** Partly right. The trained model beat the baseline overall, but only by 0.033 accuracy, which is one post out of 30, so the margin is thin. The biggest gain was on `analysis` (+0.114), as I predicted. But the trained model did worse than the baseline on `hot_take` (0.500 vs 0.560).

**What my definitions were worth:** The baseline above uses my label definitions. With bare label names only, the same model scored 0.467 accuracy and 0.383 macro F1, with 0.000 F1 on `analysis`. The definitions added +0.100 accuracy and +0.170 macro F1, mostly by teaching it what `analysis` means.

<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

Fine-tuning added very little. The trained model is ahead by 0.033 accuracy, which is a single post on a 30-post test set, so this one split can't show that the gap is real. The three-seed runs in Milestone 2 will show whether it holds. The trained model's real advantage is on `analysis`, where it learned my 'specific, checkable support' boundary better than a zero-shot model reading the definition. It lost ground on `hot_take`, which suggests the hot_take/analysis or hot_take/reaction boundary in my training labels is less clear than the definition. The baseline also takes a few seconds per post, while the trained model is near-instant, so even at similar accuracy the fine-tuned model is cheaper to run.

**Update after three seeds:** Across seeds 42, 7 and 2024 the trained model scored 0.600, 0.500 and 0.467 accuracy (mean 0.522). The baseline (0.567) was scored on only one split, so this is not a like-for-like comparison, but it is above two of my three seeds. The single-split win came from a friendly draw. Averaged over seeds, fine-tuning did not clearly beat a zero-shot model reading my definitions.



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Macro F1 | ≥ 0.70 | 0.583 | 0.404 | 0.444 | MISSED |
| 2. `analysis` recall | ≥ 0.65 | 0.900 | 0.600 | 0.600 | MISSED |
| 3. `reaction` precision | ≥ 0.85 | 0.667 | 0.000* | 0.667 | MISSED |
| 4. Overall accuracy | ≥ 0.65 | 0.600 | 0.500 | 0.467 | MISSED |
| 5. Dataset balance (each label 25–50%) | 25–50% | 32–35% | 32–35% | 32–35% | MET |

\* Seed 7 never predicted `reaction` at all, so its reaction precision is 0.

**Spread:** accuracy ranged from 0.467 to 0.600, a 13.3-point swing across seeds. That is more than the ~10-point threshold, so 200 examples (30 test posts) is too small for a stable measurement.

**Device:** cuda (NVIDIA GeForce RTX 4050 Laptop GPU), torch 2.14.1+cu130. Each seed trained in about 3 seconds.

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 9 | 1 | 0 |
| **hot_take** | 3 | 5 | 2 |
| **reaction** | 2 | 4 | 4 |

*Seed 42, as printed by section 7.*

Summed across all three seeds (90 test predictions):

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 21 | 9 | 0 |
| **hot_take** | 7 | 20 | 3 |
| **reaction** | 7 | 17 | 6 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

In seed 42, 4 real `reaction` posts were called `hot_take` and only 2 went the other way. Summed over all three seeds it is clearly one direction: 17 of 30 real `reaction` posts were called `hot_take`, versus only 3 `hot_take` posts called `reaction`. The model has not learned the reaction/hot_take boundary, and when a post carries strong opinion it defaults to `hot_take`.



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Macro F1 | ≥ 0.70 | MISSED | Best seed was 0.583; none of the three reached 0.70. |
| 2 | `analysis` recall | ≥ 0.65 | MISSED | Seed 42 hit 0.90, but seeds 7 and 2024 were 0.60. The target has to hold on all three. |
| 3 | `reaction` precision | ≥ 0.85 | MISSED | 0.667, 0.000, 0.667. Seed 7 never predicted `reaction` at all. |
| 4 | Overall accuracy | ≥ 0.65 | MISSED | 0.600, 0.500, 0.467; none reached 0.65. |
| 5 | Dataset balance | each label 25–50% | MET | 70/66/64 = 35/33/32%, all inside the range. This is a property of the dataset, so it is identical on every seed. |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

**Criteria 1 (macro F1) and 3 (`reaction` precision): the reaction/hot_take boundary.** Summed over three seeds, 17 of 30 real `reaction` posts were predicted `hot_take`, and seed 7 never predicted `reaction` at all. Only 3 `hot_take` posts went the other way, so the confusion runs one way. My likely cause is a boundary I applied inconsistently: under my definitions a soccer reaction often contains a judgment ("what a goal, best striker in the league"), which also fits `hot_take`.

**Criterion 4 (accuracy) and all misses: undertraining.** Eval loss only fell from about 1.09 to 1.04–1.07 over 3 epochs. Random guessing on three labels gives 1.0986, so the model barely moved from chance. Mean confidence was 0.36–0.37 (chance is 0.33), and almost the same when right (0.37) as when wrong (0.36).

**Criterion 2 (`analysis` recall): a hard pair plus a tiny test set.** Seed 42 reached 0.90, but seeds 7 and 2024 were 0.60, with 4 analysis posts called `hot_take` in each. With 10 analysis posts per split, each post is worth 0.10 recall.

**Underlying all four:** accuracy swung 13.3 points across seeds (0.467 to 0.600), so with 30 test posts the measurement itself is unstable.



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->

**What I meant vs. what the model learned:** I meant `reaction` to capture in-the-moment emotional responses and `hot_take` to capture unsupported claims, but the model learned something closer to "strong opinionated language means hot_take", which is why 17 of 30 reactions were called hot_take across seeds. It did best on `analysis` (21 of 30 correct summed over seeds), which suggests it may be keying on surface cues like numbers rather than on whether the fact carries the argument.



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
