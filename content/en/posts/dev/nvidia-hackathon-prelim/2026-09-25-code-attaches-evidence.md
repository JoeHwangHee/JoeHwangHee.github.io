+++
date = '2026-09-25T18:09:00+09:00'
draft = false
title = "[2026-09-25] Don't Edit the Model's Writing; Let Code Append"
summary = "Many reports had nearly correct verdicts but were missing required evidence. We decided to leave the model's verdicts and explanations as they were, have code append only the missing required evidence from verified values, and disclose that we did so."
tags = ['NVIDIA Hackathon']
aliases = ['/posts/dev/nvidia-hackathon/2026-09-25-code-attaches-evidence/', '/posts/dev/nvidia-hackathon/preliminary/2026-09-25-code-attaches-evidence/']
+++

The results of the first MVP round (20 synthetic cases) were strange. The verdicts were almost all correct, but the required-evidence check dropped sharply.

---

## Background: The Model Only Looked After Its Own Evidence

The model put only one or two change-rate claims about its own series into the report. Evidence essential to the verdict, like peer-country comparisons or composition-effect decomposition, it had already received through tools yet didn't write into the report. We had already confirmed that fixing things through instructions was weak.

## Options

| Plan | Judgment |
|---|---|
| Add guidance to the instructions | Dropped — instructions were a weak tool |
| Have code delete or edit the model's prose | Dropped — editing model output blurs what is being evaluated |
| Enforce an output schema | Not possible — this hosted endpoint rejected output schema enforcement |
| Leave it as is | Dropped |
| **Have code append** | **Adopted** |

My answer was, "Go with having code append."

## Rules

- Apply the same rules in every mode.
- For each piece of required evidence a per-signal verdict needs, if there is no claim showing it, code picks it **from the verified values of evidence already received** and appends it.
- If it isn't in the received evidence, nothing is appended.
- The verdicts, claims, and explanations the model wrote are not changed.
- The fact of appending is recorded in the trace (the execution trace record) and disclosed per mode.

## The Orchestrator's Mistaken Explanation

About an hour later, a problem surfaced. The orchestrator had earlier explained that "the headline metric doesn't change," but when appending was applied to recorded runs, in one `freeform` report **an appended claim backed up a number in the model's prose.** That meant the headline metric could change. The orchestrator corrected its explanation right away and asked again.

I answered that the appended values were also values already in the evidence the model had received when making its judgment, so they could count as evidence. In exchange, we decided to **disclose both the report-level error rate before appending and the number of prose statements supported thanks to appended claims.** The risk of numbers with different meanings happening to have the same value is exposed through disclosure.

## Decisions in the Same Vein

That night and the following early morning, we decided a few more things on the same principle.

- Code aggregates `review_status` from per-signal states using priority rules
- For signals where the draft verdict and the rule reference value are both "hold for data," don't raise missing-tool findings
- When invalid results kept recurring because '0' appeared in the prose, append a weight-share claim with a value of 0 when the unit-price verdict is a hold

## What This Choice Meant

We changed the disclosure wording too. Evidence-satisfied processing accuracy measures **not "whether the model takes care of it on its own" but "whether the system shows the required evidence in the report."** We don't present it as the model's achievement. If you put in a mechanism that makes the numbers look better, you also have to show how much that mechanism changed the numbers.
