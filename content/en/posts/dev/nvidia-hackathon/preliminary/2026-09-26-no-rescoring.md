+++
date = '2026-09-26T14:25:00+09:00'
draft = false
title = '[2026-09-26] No Rescoring: Grade A Claim Withheld (Reference Value)'
summary = "On the sealed real data, the report-level error rate was 0/40 for full mode and 11/40 for freeform. But an input format error left the automatic grade A determination unmet, and under the 'score only once' rule we did not rescore."
tags = ['NVIDIA Hackathon']
aliases = ['/posts/dev/nvidia-hackathon/2026-09-26-no-rescoring/']
+++

We ran and scored the two sealed sets once each. The numbers were good. Yet in the results table we wrote "Grade A claim withheld (reference value)."

---

## Results

The headline metric is the report-level factual-claim error rate on 40 sealed real-data cases (27 distinct time series).

| Mode | Report-level error rate (Wilson 95%) |
|---|---|
| `freeform` (model writes numbers directly) | 11/40 = 27.5% (16.1%–42.8%) |
| `full` (verified-value filling + Critic + validator) | 0/40 = 0.0% (0.0%–8.8%) |

The two intervals don't overlap, so under the rulebook's rules, `full`'s error rate is "lower." In a pre-registered secondary analysis, the 95% interval for the paired difference was +13.1 to +42.8pp, and the McNemar exact test p was 0.001. These values include all failed and invalid runs in the denominator.

## The Problem: Automatic Determination Unmet

The scorer's automatic grade A determination was "unmet (condition 3)." The cause was that the orchestrator had put a **sentence** instead of true/false into the "scorer pre-verification" field of the run-conditions input file. In substance, the condition was met. All 203 of the scorer's own tests passed right before scoring. Only the format was wrong.

There were a few more input errors during scoring: the format of an operator value (it accepts exactly "match," but a sentence was passed), the host lacking a snapshot build, and so on. These were calls where the scorer stopped without producing output, and I approved correcting only the single offending value in each case.

## Options

| Plan | Judgment |
|---|---|
| Fix the input file and rescore | Dropped — rulebook B6 "score only once," B5 "don't adjust in your favor after seeing the results" |
| **Keep as is, label it "Grade A claim withheld (reference value)"** | **Adopted** |

The orchestrator flagged an exceptional rescore as an option while recommending keeping things as they were. I didn't change anything.

## What This Choice Meant

We had already seen the results. Fixing the input file and rescoring while knowing the results were good would itself be "adjusting in our favor after seeing the results." Looking at the purpose of the rule rather than its wording, the answer was clear. "If not all conditions are met, we withhold the grade A claim," set on the first day of design, was a sentence written for exactly this moment.

The results table and the submission didn't claim grade A and presented the headline metric value as a reference value. The format error that caused this went on the list of open issues.

## What We Disclosed Alongside

- That the real-data verdict distribution was skewed toward hold for data (`full`: keep under review 17, monitoring 0, hold for data 23)
- The number of claims appended by code, and the error rate before appending
- A Korean-quality sample check of 25 cases: 0 assertive expressions in the fraud, illegality, or origin categories

After scoring and recording the seed disclosure, we re-checked all 202 hashes and committed the sealed originals to the repository.
