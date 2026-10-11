+++
date = '2026-09-25T08:41:00+09:00'
draft = false
title = '[2026-09-25] Nail Down the Thresholds Before Seeing the Results'
summary = "We approved 14 morning decisions at once, including the judgment policy policy_v1 and the sampling method. The point was to prevent picking thresholds after seeing the results."
tags = ['NVIDIA Hackathon']
+++

While the two tracks implemented units overnight, decisions piled up. In the morning, the orchestrator posted a decision request with 14 items.

---

## Background: Thresholds Before Results

If you don't set the alert thresholds (unit price 30%, share 10pp), the verdict interpretation, and the real-data verdict distribution before the results, you'll want to adjust them in your favor after seeing the numbers. So we decided to freeze the policy **before** running the first real-data detection.

## What We Decided

My answer was one line: "All as recommended, except for 10, which is (b)."

The main items were these.

- **`policy_v1`**: unit price 30%, share 10pp, a minimum of 100 USD and 10 kg for unit-price signals only, a rounding tolerance, and the verdict interpretation
- **Accepting the real-data verdict distribution before the results**: on the development real data, hold for data was expected to be 59% or more, with zero "monitoring" verdicts
- **The sampling method for the sealed set**
- A per-case token limit of 128,000 and 11 cause-classification codes
- Pushing the rulebook freeze time back by a day
- The working method: "If the preceding step is in its final stage and the evidence is in, move to the next step even before the planned time"

## Sampling: What We Dropped

| Method | Why dropped |
|---|---|
| Python `random` | Results can differ across Python versions |
| Stratified sampling | Requires defining yet another weighting rule |
| First 40 cases | Not random |
| **Ranking by sha256 of the seed file bytes and case ID** | **Adopted** — same result wherever it runs |

Only for decision 10 did I pick (b) instead of the recommendation. That option leaves the data contract's run-name rule alone and just adds a `--run-name` option to the CLI. I thought it was better not to shake the contract.

## About the Number 30%

To be honest, there's no economic basis for choosing 30%. The development plan defines this value as "not an economic risk boundary but a value that generates investigation candidates." What actually moved the number of alerts wasn't the threshold but the 10 kg minimum weight. In months under 10 kg, rounding to whole kilograms alone made the unit price swing.

The reason for so many holds for data wasn't the policy values either. Between the two months, the composition of 10-digit detailed items changed, creating many conditions where composition-effect decomposition wasn't possible. We decided to accept this before the results and disclose it.

## What This Choice Meant

Right after the decision, we **committed first** the method for picking 20 real-data MVP cases (the top 20 by sha256 ranking), and only then ran real-data detection for the first time. There were 221 alerts, the same as expected at policy approval. The commit order itself became evidence that "we didn't pick cases after seeing the results."
