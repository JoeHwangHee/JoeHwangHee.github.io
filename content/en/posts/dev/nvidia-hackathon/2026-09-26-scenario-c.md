+++
date = '2026-09-26T06:45:00+09:00'
draft = false
title = '[2026-09-26] The Submission Scope Is Scenario C'
summary = "The MVP was done, but I asked whether it was enough to submit. Comparing an MVP level (A), a full application (B), and something in between (C), we chose C, which changes the impression of completeness while protecting the time for the sealed evaluation."
tags = ['NVIDIA Hackathon']
+++

Around 4 a.m. on September 26, we checked off all 9 items on the MVP pass checklist. But with two days left until the submission deadline, a question came up. **Is it okay to submit at this size?**

---

## Background: Self-Scoring Round 1

We ran self-scoring round 1 with the evaluation skill. Three of the six gates were FAIL, so not even a score band came out. The cause wasn't missing features. It was a plumbing problem, with no way for the evaluation harness to receive values only the operator knows, plus the lack of a root README and a command to reproduce without a key.

When I asked whether the MVP size was fine, the orchestrator didn't make new rules; it gathered the roadmap, the rulebook, and the competition research documents into a submission scenario document.

## Options

| Scenario | Content | Estimated remaining time |
|---|---|---|
| A | MVP level: README + keyless reproduction | 0.5–2.5 hours |
| B | Full application: 3 screens + mock approval + expanded reproduction bundle | 11–15 hours |
| **C** | **A + 1 screen** | **3–5.5 hours** |

I asked about the task lists and time differences of the three scenarios, then chose C.

## Why C

- **It changes the impression of completeness at the lowest cost.** Build one screen, "Case View," with Streamlit (a Python web UI library) and add charts.
- **It protects the time for the sealed evaluation, the results table, and submission prep.** Choosing B would push back the sealed scoring scheduled for that day.
- **The screen is outside the scoring path.** It can be built even after the rulebook is frozen.

Screens 2 and 3, the mock approval, and a one-click run environment were cut following the roadmap's "order of reduction." But we left the door open to add them if the results table finished on schedule and there was slack. The sixth run on development real data was to start when I gave the word (I never did).

## What We Put In Alongside

That same early morning, I approved two CLI options. `--conditions-extra` merges run conditions that only the operator knows, and `--replay` runs end to end **without a key** using the committed synthetic fixtures and recorded model responses. Judges needed to be able to reproduce it within 5 minutes without a key. Both options are plumbing that doesn't change the scoring rules.

## What This Choice Meant

This decision also set what we would claim in the submission. Mention the one screen; don't mention what wasn't built. But this scope changes once more that evening. It was after I looked at the screen myself.
