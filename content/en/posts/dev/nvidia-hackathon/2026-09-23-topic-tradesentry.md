+++
date = '2026-09-23T20:00:00+09:00'
draft = false
title = '[2026-09-23] Shelving HSGate for TradeSentry'
summary = "After a meeting with a teammate, we switched topics from HSGate, which we had researched all day, to TradeSentry, which investigates sudden shifts in Korea Customs Service import statistics."
tags = ['NVIDIA Hackathon']
+++

On September 23, the topic changed twice. In the morning we widened from security-type candidates to real-world work candidates, and in the evening we set down the candidate we had dug into deeply all day.

---

## Background: The Candidates Leaned One Way

The top candidates picked the day before, like "auto-generating agent permission policies" and "MCP supply chain auditing," were tools for organizations that build agents. They were strong on depth of NVIDIA technology use but weak on industrial value and Korean context. So we brainstormed again around real-world, everyday candidates, and the top pick was HSGate. It's a gate that takes an imported product's description, proposes an HS code (the international commodity classification code) based on classification ruling cases, and allows the declaration to be sent only when there is both evidence and a customs broker's approval.

At this point we also set a common rule for judging OpenShell use. **"We ran it in a sandbox" is just hygiene.** You can only call it use when the core flow structurally depends on features like policy blocking or credential separation.

For HSGate, we wrote a design document and researched along seven lines until 7 p.m., including data validity, an evaluation-only re-review, and cross-checking against the NVIDIA stack's official documentation. Because the ruling cases could serve as ground-truth labels, it was the only candidate that could earn number grade A.

## Options

| Candidate | Strength | Weakness |
|---|---|---|
| Security/DevOps | Depth of NVIDIA technology use | Weak industrial value and Korean context |
| HSGate | External ground-truth labels (grade A) | The classification call itself is weighty and carries heavy liability |
| **TradeSentry** | Reproducible from public statistics; an agent is essential to the investigation flow | Ground-truth labels must be designed separately |

## What We Chose: TradeSentry

After a meeting with a teammate in the evening, we switched to TradeSentry. It flags as alerts cases in Korea Customs Service import statistics where the per-kg unit price or a partner country's share changed sharply from the same month a year earlier; a Nemotron (NVIDIA's language model) investigator and a reviewer in a separate context (Critic) try to disprove it with fixed lookup tools; and then it proposes the staff member's next task as one of: keep under review, monitoring, or hold for data. It is not a tool that rules on fraud or illegality.

Interestingly, that same evening the document research reported that, on the data side, HSGate actually had the advantage. We switched anyway. We didn't keep the meeting's contents in the repository.

## What This Choice Meant

The HSGate research wasn't wasted. The results of cross-checking the NVIDIA stack against official documentation (the scope of L7 policy rules, and the fact that Korean isn't explicitly listed on the Nemotron model card) carried over directly into the development plan's risk list and the "honest claims" rule.

## Looking Back

I still regret that the reason for the topic change isn't in the record. It means the design's starting point can't be explained from the documents. In later projects, we kept the rule from the start: **record decisions in decision record files, not in chat.**
