+++
date = '2026-10-06T21:00:00+09:00'
draft = false
title = '[2026-10-06] The Finals Guide: Drop the Rituals, Keep the Principles'
summary = "Hearing we'd made the finals, we condensed the preliminary round's development order and decision-making into a topic-agnostic guide for a one-day final whose topic we didn't know. Carry six days of rituals into one day as they are, and you won't finish."
tags = ['NVIDIA Hackathon']
aliases = ['/posts/dev/nvidia-hackathon/2026-10-06-finals-guide/']
+++

We passed the preliminary round and made it to the finals. The finals were a single day, October 7, and the mission would be revealed that morning.

---

## Background: What Do You Prepare When You Don't Know the Topic?

I created a folder for the finals next to the preliminary-round repository and asked for a guide for the agents to read. The idea was to draw on the preliminary round's development order, decision-making, and NVIDIA development foundation. When the agent first tried to assume a topic, I said: "I don't know what the finals topic will be. This is for referencing the approach."

## What We Dropped and What We Kept

The preliminary round ran for six days on 11 planning documents, sealing outside the repository, some 90 decision record files, and an independent review of every PR. Carry that into one day as it is, and you won't finish.

| Preliminary approach | In the finals | Reason |
|---|---|---|
| 11 planning documents | One guide (later split by document) | We don't know the mission, so the product part can't be written |
| Sealed evaluation and scoring only once | Reduced to **Freeze 1 (evaluation defined before implementation) and Freeze 2 (scoring script before measurement)** | The principle (don't change the rules after seeing the results) stays |
| One decision record file each | Append one row at a time to a single table | Dozens come up in a single day |
| Independent review of every change | Code gets reviewed; **commits that only change state or decision records go straight to main after a secrets scan** | Attaching reviews to records that change several times a day only costs time |
| 429s, key rules, sandbox pitfalls | **Kept as is** | Things we learned by paying for them in the preliminary round |

## What We Fixed in Review

The draft guide got a FAIL in independent review. There were three issues.

- It was ambiguous who triggers the fallback path (an alternative when blocked). → Only approved steps happen automatically, with immediate notification.
- There was only one point at which evaluation is frozen. → We split it into Freeze 1 before implementation and Freeze 2 before measurement.
- The locations of git and `.env` weren't set.

## The Day's Timetable

We received the organizers' official timetable (running 10:30–17:30, pitching at 17:30) and built ours with 10:30 as 0:00. Freeze 1 at 12:10, once mission intake and the change of direction were done; feature freeze at 15:00; rehearsal from 16:30. There was a proposal to freeze even earlier, but we judged that **evaluation has to be locked after the change of direction** for it to mean anything.

The night before, we also issued a new key so the preliminary-round key could be revoked. We updated the two credential bundles registered in the sandbox with the new key.

## What This Choice Meant

The guide covered not "what to build" but "how to decide." Even if the topic changes, the way of deciding can be used as is: lock evaluation first, leave decisions as records, and when blocked, set a time cap and ask a person.

But this guide faced a big test that morning. Once the mission was revealed, I would end up saying, "Let's start by lowering the authority of the docs we generated."
