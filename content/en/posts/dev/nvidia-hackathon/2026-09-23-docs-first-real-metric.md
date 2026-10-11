+++
date = '2026-09-23T22:40:00+09:00'
draft = false
title = '[2026-09-23] Documents Before Code, and a Headline Metric on Real Data'
summary = "Instead of jumping straight into implementation, we decided to write the planning documents first. The headline metric became the factual-claim error rate on a sealed set of real data, not synthetic data."
tags = ['NVIDIA Hackathon']
+++

At 10 p.m. on September 23, opening the design session, I asked the Claude orchestrator for five things: a development plan, an evaluation rulebook, a roadmap for running two tracks at once, a test plan for the September 25 MVP, and permission to use sub-agents and Codex.

---

## Background: Five Days Left

There were five days until the deadline. The temptation to write code right away was strong. But the rubric set the day before required "evaluation first, features later." If you change the rules after seeing the results, nobody trusts the numbers.

The orchestrator laid out eight items, decisions to carry forward and recommended defaults, and I confirmed four of them as core decisions.

## Decision 1: Documents First, Implementation Next

| Option | Judgment |
|---|---|
| Implement right away | Dropped — the evaluation rules get dragged along by the results |
| **Planning document set first** | **Adopted** — put the evaluation rules and baselines ahead of features |

The cost of this decision was clear. That night and the following early morning went to documents only, and the September 25 MVP schedule got tight.

## Decision 2: Claude Sub-Agents on Both Tracks

The original division-of-work agreement had two people split and sign off on a model track (judgment policy, investigation flow, NVIDIA integration) and a data track (collection, metrics, evaluation data, scorer). We changed this so that **Claude sub-agents carry out both tracks, with separate domain-review agents spun up.** That sentence is a decision I typed in myself.

## Decision 3: Harness → Skill → Sandbox

We set the way the NVIDIA stack fits together as: the NemoClaw harness calls an Agent Skill, and the skill runs a CLI inside the OpenShell sandbox. The alternatives, a "scaled-down OpenShell-centric" version or "making the investigator itself OpenClaw," made it hard to keep both NemoClaw usage and limit enforcement.

## Decision 4: The Headline Metric Uses Real Data

| Option | Judgment |
|---|---|
| 40 synthetic cases | Dropped — if we make it and we score it, it's grade D, and even at best it can't get past C |
| **Factual-claim error rate on a sealed set of real data** | **Adopted** — real statistics provide an external ground truth |

We set the headline metric as a comparison, on the sealed real-data set (`real_sealed`), of the **report-level factual-claim error rate** between `freeform`, where the model writes numbers directly, and `full`, which has the complete validation structure.

## What This Choice Meant

The decisions that night settled "what to prove" before "what to build." The feature list was trimmed to what that proof required.

## Afterward

By early the next morning, 11 planning documents (development plan, rulebook, data contract, parallel development rules, roadmap, and more) had been merged via PRs. Domain-specific review agents and Codex looked at each document, and blocking findings such as "deduplication of the all-countries share denominator is missing, so the denominator doubles" were caught before implementation.
