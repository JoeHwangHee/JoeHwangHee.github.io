+++
date = '2026-10-07T10:30:00+09:00'
draft = false
title = '[2026-10-07] Starting Fresh Instead of Reviving the Stuck Demo Sandbox'
summary = "A mistake the night before, turning the Docker virtual machine off right after turning it on, left the demo sandboxes stuck in Error. When both recovery and re-onboarding were blocked, we abandoned the NemoClaw path and went with a new sandbox that uses OpenShell alone."
tags = ['NVIDIA Hackathon']
+++

On the morning of the finals, both demo sandboxes built in the preliminary round were in the Error state. The cause was a small mistake the night before.

---

## Background: The Price of Turning It Off Right After Turning It On

The night before, to check the sandbox list, the agent turned colima (a virtual machine that runs Docker on a Mac) on and then immediately off. At that moment, the gateway was bringing up the sandbox containers, and the containers were forcibly terminated mid-start. The gateway recorded this as Error.

The OpenShell CLI had no command to get out of Error. Start only works from the "stopped" state, and stop only works from the "ready" state. That night, within a 15-minute timebox, we tried five things, all of which failed, so we stopped and only left a record. There was one lesson. **When shutting down, stop the sandboxes first, then turn off colima. Don't turn off colima right after turning it on.**

## Morning: Recovery Attempts

- Restarting the gateway service: it reconnected within 10 seconds, but the sandboxes stayed in Error.
- NemoClaw's recover: the containers came up fine, but the Error flag in the gateway's store didn't clear.
- Deleting the demo sandbox and onboarding fresh: it stopped at step 3 of 8. A command we had run as a status check had written another leftover sandbox into NemoClaw's local registry without any details, and the compatibility check for the shared inference path failed because of that entry.

## Options

At 10:30, when the mission was revealed, three options remained.

| Plan | Content | Judgment |
|---|---|---|
| (a) | Back up the registry, delete only the problem entry by hand, then re-onboard | Dropped — if it got stuck again, it would cost even more time |
| (b) | Give a session in another window file-edit permission and have it fix things | Dropped — the same risk, plus wider permissions |
| **(c)** | **Put off the NemoClaw path and use a new sandbox with OpenShell alone** | **Adopted** |

My answer was, "Let's go with c for the sandbox."

## Why (c)

Looking at the mission document, of the 100 judging points, 40 were "depth of NVIDIA Agent technology use — OpenShell," and "must include OpenShell components" was a mandatory condition. **NemoClaw wasn't among the mandatory conditions.** Rather than spending time reviving the blocked NemoClaw path, going deep on OpenShell, where the points were, was the right call.

## Another Decision at the Same Time

At the same time, we decided one more thing. "Let's start by lowering the authority of the docs we generated." The mission differed from what we had assumed in advance (judging weights, deliverables, cloud credits), and development time was already ticking. We kept the secrets rules and the honest-claims rules as they were, and decided to use procedural rules only when needed.

## What This Choice Meant

Not losing mission time in the morning trying to fix the previous night's mistake: that was the heart of this decision. We compared the cost of reviving what was broken with the cost of starting fresh, and chose what to drop based on **what the judging criteria actually required**. The lesson from the mistake went into the finals guide's list of pitfalls and the "order for shutting down and starting up" in the operations document.
