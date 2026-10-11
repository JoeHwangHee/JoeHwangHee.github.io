+++
date = '2026-10-07T14:30:00+09:00'
draft = false
title = '[2026-10-07] Switching the Router from Claude to Nemotron'
summary = "We attached the Claude API to the router that judges a question's weight, then realized that without a Claude key in the evaluation environment every request would end as 'unable to respond,' and switched to Nemotron."
tags = ['NVIDIA Hackathon']
+++

The structure we settled on around noon was this. When a question comes in, first judge its weight; if it's light, answer right away; if it's heavy, run it through the route and origin research structure.

---

## The First Decision (12:00)

Here's the original text of my instruction.

> 1. When a question comes in, attach the Claude API to check the question's weight and proceed
> 2. The Claude API only judges the question's weight (on response failure, fall back; unable to respond)
> 3. For heavy questions, use the developed structure
> 4. The database stores the indexed data (as JSON inside the project)

Then we decided two more things. Light questions are answered by Nemotron without materials, tagged "no material evidence (general guidance)." If the Claude judgment still fails after one resend, it doesn't guess; it ends with "unable to respond."

## The Problem: The Evaluation Environment Has No Claude Key

As implementation went on, a problem came into view. If the environment where judges pull and run our repository has no Claude key, the router fails, and by the rules, **every request ends as "unable to respond."** The core built with NVIDIA technology never gets called even once.

## Options

| Plan | Judgment |
|---|---|
| Skip the Claude judgment via an environment variable (the recommended plan) | Dropped — skipping it makes the weight judgment itself disappear |
| Switch automatically only when there's no key | Dropped — both paths have to be maintained and tested |
| Leave it as is | Dropped |
| **Replace the router with Nemotron** | **Adopted** |

My answer was short. "Change to Nemotron instead of Claude (top priority)."

## What We Changed

- The product doesn't call Claude.
- We deleted the Claude Messages API allow blocks from the two policy files, and we don't attach a Claude provider to the sandbox.
- We also renamed the field in the run records and the call-limit name to `router`.

Removing Claude from the policy cut one entry from the outbound allow list. The fewer the paths leading outside, the easier it is to explain the permission design, too.

## What This Choice Meant

**A demo has to run in someone else's environment.** A structure that only runs on the developer's laptop means nothing in judging. And at an NVIDIA hackathon, a structure that hands the core judgment to another company's model, even if technically possible, was weak as a story. Replacing the router was a decision that fixed reproducibility and the story together.

## Afterward

Late in the afternoon, we also made it possible to use the vLLM endpoint the team had brought up on Brev (NVIDIA's GPU cloud). Since it was a change after feature freeze, we kept the scope narrow. We added just one setting for choosing the key variable name, and one vLLM block in the policy file that only the app's Python can use. We confirmed that inside the sandbox vLLM answered a light question, and that the OpenShell log recorded that path as allowed.
