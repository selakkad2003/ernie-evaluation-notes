# Calibration Practice — Getting 3 Reviewers to Score the Same Way

> Rubric design is the easy part. Getting reviewers to score consistently is where evaluation projects die. This is the practice that worked in the Baidu ERNIE Mentor Program.

---

## The problem

Two reviewers given the same response and the same rubric will score differently. The gap is typically:

- **0.3 points (out of 5)** on dimensions where anchors are tight (factuality)
- **0.8 points** on dimensions where anchors are loose (calibration, reasoning)

If you don't actively manage this drift, your "aggregate score 3.8" is unreliable. The same model could score 3.5 or 4.1 depending on who reviewed it.

---

## Practice 1: Anchor examples per score level

For EVERY rubric dimension at EVERY score level (0–5), have a **canonical anchor example** that all reviewers have read and discussed.

The anchor is not "what a 4 looks like in general." It's "this specific prompt + this specific response = 4 because [reasons]."

We had ~30 anchor examples for the 5-dim × 6-score-levels rubric (30 ≠ 5×6 because some dimensions are binary). Reviewers re-read them before each scoring session.

**Anchor template:**

```
DIMENSION: Reasoning
SCORE: 4
PROMPT: [exact prompt]
RESPONSE: [exact response]
REASONING FOR THIS SCORE:
- What pushes it above 3: [specific feature]
- What pushes it below 5: [specific gap]
EDGE NOTE: [common confusions with adjacent scores]
```

Without anchors, reviewers use intuition. With anchors, they use comparison. Comparison is more reliable than intuition.

---

## Practice 2: Triple-review on edge cases

Any response that scores **2.5–3.5** on any dimension gets two more independent reviewers.

Why this range: it's where reviewer disagreement is highest. Below 2.5 ("clearly bad") and above 3.5 ("clearly good") reviewers agree. The middle is where the rubric's ambiguity lives.

Cost: ~15% of scored items triple-reviewed.
Benefit: median disagreement on triple-reviewed items drops from 0.8 to 0.3 after discussion.

**The rule:** if 3 reviewers' scores span more than 1.0, schedule a 15-minute discussion. Don't just average — discuss until either (a) all agree on a score, or (b) a new anchor example is added because the case revealed a gap in the rubric.

---

## Practice 3: Weekly recalibration meetings

Every Friday, 30 minutes. 5–10 items from the week, randomly sampled, re-scored by all reviewers in the room. Compare scores. Discuss disagreements. Update anchors if needed.

This is the highest-ROI activity in the entire eval workflow. Skipping it means drift accumulates silently until the dataset is unusable.

**Format that worked:**

1. Each reviewer scores 5–10 items independently before the meeting (10 min).
2. In the meeting, project scores side-by-side per item.
3. For items with std-dev > 0.5: discuss. The reviewer who scored highest explains their reasoning. Then the reviewer who scored lowest. Then a third reviewer arbitrates.
4. If the rubric is clear, the "wrong" reviewer updates their understanding.
5. If the rubric is ambiguous, the rubric is updated and added to the anchor set.

---

## Practice 4: Confusion matrices, not just averages

After every eval cycle, generate a confusion matrix per dimension showing how often Reviewer A and Reviewer B disagreed.

```
Reasoning dimension — Reviewer A vs. Reviewer B (n=200 items)

       B=0  B=1  B=2  B=3  B=4  B=5
A=0:    8    2    0    0    0    0
A=1:    1    9    3    0    0    0
A=2:    0    2   28    5    0    0
A=3:    0    0    4   42    7    1
A=4:    0    0    0    4   38    4
A=5:    0    0    0    0    2   40

Inter-rater κ (Cohen's kappa) = 0.78  (substantial agreement)
```

Targets:
- **κ ≥ 0.80** for binary dimensions (factuality, safety)
- **κ ≥ 0.70** for graded dimensions (reasoning, fluency, calibration)

Below those: the rubric needs anchor improvement. The reviewers aren't bad — the instrument is.

---

## Practice 5: Pre-eval calibration warmup

Before each major eval session (e.g., scoring 200 model responses), run a 15-minute calibration warmup: score 10 anchor items, compare to the known-correct scores, discuss any deltas.

This is like a tuning fork for a string instrument. The instrument is the reviewers' shared mental model.

---

## Practice 6: Adversarial blind sets

Periodically include items where the "correct" score is known to the eval lead but not to the reviewers. After the session, compare reviewers' scores to the known correct scores.

This is how you detect reviewer drift over weeks. Without blind sets, drift is invisible until your evaluation outputs start contradicting prior outputs in ways you can't explain.

We rotated 5 blind items into every 50-item batch. Reviewers don't know which.

---

## Practice 7: Don't average across reviewers — vote

When you have 3 reviewers and they score 3, 4, 4 on a single item, the temptation is to report the mean (3.67). The right answer is to report the **median** (4) — robust to one outlier reviewer.

Mean is sensitive to one reviewer being lazy or having a bad day. Median isn't. Confidence interval reporting (e.g., "median = 4, IQR = [3.5, 4]") is even better.

For aggregate over N items: mean of medians, not mean of means.

---

## Practice 8: Specialized reviewers per dimension

Reasoning is hardest to score well. Hire reviewers who are domain experts in the question categories you're testing.

Fluency requires native-or-near-native speakers of the target language. A non-native scorer will rate broken fluency as "fine" because they don't notice the awkwardness.

We had separate review pools for technical-reasoning categories vs. creative-writing categories. Better scores, more honest scores.

---

## Practice 9: Don't reuse the test set every iteration

If the same 200 items get scored every release cycle, the model team will learn (consciously or not) what those items look like and tune toward them.

Rotate: keep 70% of items stable across cycles (so trends are comparable), refresh 30% each cycle (so drift is detectable).

The 50-item Darija test set in [jak-ma-eval-suite](https://github.com/selakkad2003/jak-ma-eval-suite) is the stable 70%. New adversarial cases get added quarterly.

---

## Practice 10: Document the calibration history

Per-reviewer scoring averages drift over time. Track them.

```
Reviewer A — Reasoning dim — monthly average
Oct 2025: 3.4
Nov 2025: 3.6
Dec 2025: 3.5

Reviewer B — Reasoning dim — monthly average
Oct 2025: 3.6
Nov 2025: 3.4
Dec 2025: 3.2  ⚠️ trending down, schedule recalibration
```

A reviewer drifting down 0.2 points/month either means (a) model is getting worse, or (b) reviewer's standards are getting stricter. You need to know which.

Detect with blind sets: if blind-set scores from this reviewer also drift, it's (b) — recalibrate. If blind-set scores are stable but model scores drift, it's (a) — file a quality regression.

---

## What I'd push back on

Honest open questions:

1. **Inter-rater κ targets are domain-specific.** κ = 0.78 is "substantial" for general-purpose rubrics but might be insufficient for high-stakes domains.
2. **The 3-reviewer minimum** is expensive. For tight-budget evals, 2 reviewers + an arbiter on disagreements is the realistic floor.
3. **Calibration meetings drift** if not actively chaired. Without someone watching for "reviewers gradually agreeing on a wrong answer," the calibration process can lock in bias.

---

**Sami EL AKKAD** · Tsinghua SIGS AI MSc · ex-Baidu ERNIE Mentor (Oct–Dec 2025) · sam25@mails.tsinghua.edu.cn
