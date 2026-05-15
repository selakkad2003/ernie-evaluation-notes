# ernie-evaluation-notes

> Methodology notes and evaluation rubric distilled from the Baidu ERNIE Mentor Program (October–December 2025), where I served as external mentor / evaluator partnering with the Baidu LLM team on candidate-checkpoint evaluation.

[![Program](https://img.shields.io/badge/Baidu-ERNIE_Mentor_Program-blue)]() [![Year](https://img.shields.io/badge/2025-Oct--Dec-orange)]() [![Award](https://img.shields.io/badge/3rd_Prize-Baidu_Hackathon_2025-yellow)]()

---

## What this repo is

Not Baidu-confidential material. The public-facing **methodology** — prompt design patterns, scoring rubrics, multi-dimensional stress-test categories, and calibration practices — that I developed and refined during the ERNIE Mentor Program. Sanitized, anonymized, and structured so anyone evaluating LLMs in production can reuse the framework.

This is the methodology that informs the [jak.ma eval suite](https://github.com/selakkad2003/jak-ma-eval-suite) — but generalized to any LLM checkpoint, not Darija-specific.

## What I did in the program

External mentor / evaluator. Designed multi-dimension stress-test prompts and scoring rubrics that fed into calibration and release-gating decisions for the LLM team. Mapped model strengths and gaps across:

- **Financial reasoning** — earnings analysis, ratio comparisons, scenario modeling
- **Business writing** — proposals, executive summaries, market analyses
- **Educational content** — explanations across reading levels, K-12 to graduate
- **Industrial classification** — vertical-specific terminology, regulatory framings

Outputs went into the team's release-gating dashboard. Specific scoring detail and per-checkpoint comparisons are Baidu-confidential and not in this repo.

## The 5-dimension evaluation framework

| Dimension | What it measures | Typical scale |
|---|---|---|
| **Factuality** | Verifiable claims against ground truth | 0–1 binary (or % accurate) |
| **Reasoning** | Multi-step inference quality, logical coherence | 0–5 rubric |
| **Fluency** | Idiomatic register for the target audience | 0–5 rubric |
| **Calibration** | Hedge appropriately when uncertain | 0–5 rubric |
| **Safety** | Refusal of unsafe/manipulative prompts | 0–1 binary |

Aggregate score: factuality × safety × (weighted-sum-of-other-three). Multiplicative on the non-negotiables (factuality, safety); additive on the qualitative ones.

## Prompt-design patterns that worked

1. **Adversarial pairs.** For each capability, design a "happy path" prompt and an "adversarial twin" that should trigger a refusal or hedge. Score both. Imbalance in scoring = miscalibration.

2. **Multi-turn drift checks.** Don't just test single-turn. Test turn 3, 5, 8. Models often drift away from their initial persona or factuality bar as context grows.

3. **Cross-language calibration.** A prompt in Language A and its careful translation to Language B should score similarly on factuality but may differ on fluency. Differences pinpoint where the model has uneven training data coverage.

4. **Domain-specific terminology probes.** Test 10–20 obscure-but-real terms per vertical. Models that confidently fabricate definitions for fake terms ("Schneider-Heinz coefficient") fail the verifier and are not release-ready.

5. **Format-pressure prompts.** Ask for JSON, then YAML, then prose, of the same content. Models that produce inconsistent factual content across formats have calibration issues.

## Calibration practice

The hardest part of LLM evaluation isn't designing prompts — it's scoring consistently across human reviewers. The practices I used:

- **Anchor examples per dimension.** For every score on the 0–5 rubric, provide a written anchor example. Reviewers calibrate against the anchor, not their gut.
- **Triple-review on edge cases.** Anything in 2.5–3.5 range gets two more independent reviewers.
- **Weekly recalibration meetings.** Reviewers compare scores on a shared "calibration set" weekly. Drift detected → re-anchor.
- **Confusion matrices, not just averages.** A model with average 3.8 across 100 prompts can have wildly different failure modes than another model at 3.8. Always report the score distribution + categorical confusion matrix.

## Recognition

- **3rd Prize (三等奖) — 2025 Baidu ERNIE Hackathon** (AI Hardware Track, 23 finalists / 900+ teams) — for *RedFOX*, an ERNIE-4.5-powered children's AI hardware device.

## Why I'm publishing this

Most LLM evaluation methodology is locked inside companies. The 5-dim rubric, the adversarial pair pattern, the cross-language calibration check — these aren't trade secrets. They're discipline. Publishing the discipline lets other teams skip months of "we should have done this from day one."

## Citation

```
El Akkad, Sami. 2026. "Methodology Notes from the Baidu ERNIE Mentor Program 2025."
GitHub: https://github.com/selakkad2003/ernie-evaluation-notes
```

## License

CC-BY-4.0 — use the methodology freely, attribute when you cite. Baidu-confidential specifics are not in this repo.

---

**Sami EL AKKAD** · Tsinghua SIGS AI MSc · ex-Baidu ERNIE Mentor (Oct–Dec 2025) · sam25@mails.tsinghua.edu.cn
