+++
date = '2026-09-25T14:17:00+09:00'
draft = false
title = '[2026-09-25] Tuning Instructions and Flow on Development Real Data'
summary = "After a replay experiment pinpointed the root causes of reports ending as invalid on real data, we decided to also use the development real data for tuning instructions and flow. We left out the temperature, role-split, and answer-manual experiments."
tags = ['NVIDIA Hackathon']
aliases = ['/posts/dev/nvidia-hackathon/2026-09-25-real-dev-tuning/']
+++

Investigations that ran well on synthetic cases wobbled on real data. When we ran five development real-data cases in `full` mode, there were 2 invalid results and 2 failures from 429s.

---

## Background: "Why Did It Answer That Way?"

I asked about the root cause rather than the results. Why did the model answer that way? The orchestrator ran a **record replay experiment**. It recreated the model requests from two runs exactly, down to the hashes, then changed one thing at a time and sent them again.

Three causes emerged.

- Even the same request produced diverging answers at temperature 1.0.
- In cases where only the share signal had fired, the Critic kept demanding the unit-price-only decomposition.
- Numbers in the explanation sentences didn't match the factual claims, so the validator blocked them.

## Options

| Plan | Description | Judgment |
|---|---|---|
| Fix only the instructions | Edit the prompt wording | Dropped — changing wording alone had little effect |
| Run the MVP test as is | Proceed with the current version | Dropped |
| **model-1.3 + flow fixes** | Keep numbers out of explanations, and fix two spots in the flow with code | **Adopted** |

There were two flow fixes. (a) Code discards re-lookup requests that are specific to signals that didn't fire. (b) If a revision opened solely at the Critic's demand gets blocked, finish with the pre-revision draft that passed the checks.

## Why It Was My Decision

The rulebook allowed the development real data (`real_dev`) to be used only for threshold tuning and reinforcing prose patterns. Using it to tune instructions and flow broadened its purpose, in other words, a change to a shared agreement. So the orchestrator didn't choose; I did. We set the disclosure wording at the same time: "We tuned the investigation instructions and flow based on failure types seen in real_dev." In exchange, the sealed real data stays sealed to the end and is scored only once.

## Experiments We Left Out

That afternoon I asked which model was being sent what, and whether randomness was really needed, and also asked for a test that wrote an answer manual and the cost of violations into the request. We ran three experiments but put none of them in.

| Experiment | Why left out |
|---|---|
| Lowering temperature | Even when lowered, the same request produced diverging answers |
| Code for verdicts and claims, model for explanations only | `full` would effectively become "checklist + model explanation," changing the meaning of the headline metric |
| Spelling out an answer manual and violation costs | Only the kind of rule being broken changed |

In the evening, I suggested testing personas (role settings given to the model). The auditor persona did best, with 8/8 passing the validator, but the difference within the sample was small. The orchestrator recommended only recording it, and I replied, "Put the persona in too." We noted in the record that the difference was small.

## What This Choice Meant

The day's experiments came down to one sentence in the end. **Code boundaries are more reliable than instructions.** After the merge, invalid results on the five development real-data cases dropped to 0, and the remaining 3 failures were all 429s.
