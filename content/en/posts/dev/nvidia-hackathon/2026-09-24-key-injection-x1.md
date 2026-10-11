+++
date = '2026-09-24T20:10:00+09:00'
draft = false
title = '[2026-09-24] Where Does the Key Live: X1 and Interpretation A'
summary = "The first integration test, X1, confirmed where the API key is actually injected. We interpreted the key rule as 'never place it where the agent or its children can read it,' and decided to reissue the key after submission."
tags = ['NVIDIA Hackathon']
+++

On the afternoon of September 24, we ran X1, the integration test for the first day of implementation. It's a test that runs one path end to end, from NemoClaw through to the block logs. There was really only one question that mattered. **Is the API key visible to the agent inside the sandbox?**

---

## Background: No Automatic Move to the Cloud

Before starting, I approved one exception rule. The original design was to move to Brev (NVIDIA's cloud development environment) if X1 didn't work within three hours. But cases where key injection fails entirely or the outbound-traffic restriction requirement can't be met are not a time problem; they're a **security requirement problem**. Moving to Brev is itself a decision to put the key on a new cloud host. So in those cases, there would be no automatic move; I would be asked right away.

## Key Injection Methods

We ran two methods in a test sandbox that used OpenShell alone.

| Method | Result | Judgment |
|---|---|---|
| `inference.local` (a managed inference address provided by the gateway) | Response 200 | Dropped — in this version it was handled outside the sandbox policy, so any executable could use it |
| **credential placeholder rewrite** | Response 200 | **Adopted** — only a placeholder value sits in the sandbox, swapped for the real key at request time |

We chose the side where the method, path, and executable restrictions could be shown as policy. On the NemoClaw side, OpenClaw is a node script, so the executable was identified as node. We allowed node but narrowed the path to a single `POST /v1/chat/completions` to let it through.

## The Problem: A Different Party Was Swapping the Key

In the X1 review, the NVIDIA stack reviewer and the security reviewer separately pointed out the same thing. The party swapping the placeholder for the real key was not the gateway outside the sandbox, but **the proxy of a supervisor process running as root inside the sandbox**. The planning documents' statement that "the gateway inserts the key" didn't match the facts. The orchestrator confirmed this against the official documentation, paused the X1 merge, and put up two interpretations.

| Interpretation | Content | Result |
|---|---|---|
| **A** | Don't place the key anywhere the agent and its child processes (sandbox user privileges) can read it | **Adopted** — matches OpenShell's official design and is satisfied by the X1 results |
| B | The key must not exist anywhere in the container | Dropped — both OpenShell methods become violations, and a relay outside the sandbox would have to be newly designed, wrecking the schedule |

My answer was: "Go with A, and I'll reissue the key. Record it so the reissue notice comes up at wrap-up."

## What This Choice Meant

The remaining risk is someone escaping the sandbox and getting root. In exchange for accepting this risk, we limited where key copies could exist to "the designed injection paths" and decided to revoke the key once the competition was over. We also revised the wording of Absolute Rule 1 to match. The original lives in `.env`, copies only in the designed paths, and the agent does not open the locations of the copies.

## Afterward

This promise was kept. On October 11, after the finals were over, we revoked the key and deleted everything, including the gateway state folder that held key copies and `.env`.
