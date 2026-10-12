# Acceptance Criteria — TakeMeter

## 1. Macro-F1 Score

The model must achieve macro-F1 >= 0.70 on the held-out test set.

**Why this target:**
I want the classifier to perform well across analysis, hot_take,
and reaction instead of getting high accuracy by favoring one label.
A macro-F1 of 0.70 would demonstrate useful performance across
all three categories.

---

## 2. Analysis Recall

The model must achieve analysis recall >= 0.65.

**Why this target:**
The most useful feature of TakeMeter is recognizing substantive
soccer discussions. I chose 0.65 because missing more than 35%
of evidence-backed comments would make the classifier unreliable
for finding meaningful analysis.

---

## 3. Reaction Precision

The model must achieve reaction precision >= 0.85.

**Why this target:**
I want the model to be particularly careful when labeling comments
as reactions. Incorrectly dismissing meaningful discussion as
an emotional reaction would reduce the tool's usefulness.

---

## 4. Overall Accuracy

The model must achieve overall accuracy >= 0.65 on the held-out
test set.

**Why this target:**
My dataset has three nearly balanced labels, so random guessing
would achieve roughly 33% accuracy. A target of 65% would require
the classifier to identify substantially more than chance-level
patterns in soccer comments.

---

## 5. Dataset Balance

Each label must represent at least 25% and no more than 50%
of the complete labeled dataset.

**Why this target:**
My dataset contains 200 comments: 70 hot_take, 66 analysis,
and 64 reaction. Keeping each category between 25% and 50%
provides enough examples of each type while preventing one
label from dominating training.
