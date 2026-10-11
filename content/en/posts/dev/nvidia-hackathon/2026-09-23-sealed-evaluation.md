+++
date = '2026-09-23T23:44:00+09:00'
draft = false
title = '[2026-09-23] Score Only Once: Designing a Sealed Evaluation'
summary = "We decided to seal the evaluation data outside the repository and commit only its hashes. So that the same cases would never be used both for fixing and for scoring, we split the real data with a fixed random seed before development."
tags = ['NVIDIA Hackathon']
+++

Late in the design session, an independent review agent that hadn't seen the conversation asked: "What has to be settled first for the headline number to be recognized as grade A?" The answers became this project's evaluation structure.

---

## Background: If Developers Can See the Answers, the Numbers Mean Nothing

Model-track agents work by reading the repository. If evaluation data sits in the repository, it becomes visible to development agents the moment a PR is merged. With a human developer, "just don't look" is the end of it, but with agents you have to block it structurally.

## Three Decisions on Evaluation Reliability

| Decision | Rejected alternative | Why rejected |
|---|---|---|
| Score all factual claims deterministically | Score only numeric fields | TradeSentry fills numbers from verified values, so it structurally gets 0%, which looks like a foregone result |
| | AI scorer | Risk of grade D |
| Run the scoring targets directly via the CLI inside OpenShell | Scoring through NemoClaw | An extra harness layer blurs the differences between comparison groups |
| Sealed data outside the repo, commit only hashes | Evaluation folder inside the repo | Development agents can see it as soon as it's merged |

Since everything runs under the same OS user permissions, there's no way to technically block access to the sealed folder completely. We didn't hide this limitation; we wrote it in the documents. Hashes reveal tampering but don't prevent viewing.

## We Split the Real Data First

I asked, "Is it a problem to use public data that already exists for the headline number?" The orchestrator answered that using official data is actually a reason for grade A; the problem is **using the same cases both for fixing and for scoring.**

| Option | Judgment |
|---|---|
| Use all the real data and disclose that it "was exposed during improvement" | Dropped |
| **Split first with a fixed random seed** | **Adopted** |

There were 1,267 comparable combinations and 771 unit-price ±30% alerts, so splitting cost almost nothing. The next day, we split the 64 item × partner-country time series once with a public seed and fixed them as 21 for threshold tuning and 43 for sealing. We don't re-split after seeing results.

## Codex Review Meetings and the Conditions for a Grade A Claim

In a split terminal pane, we held two read-only review meetings with Codex. Of nine findings, we incorporated items such as a cap on MVP run volume, the gap between sample size and prose scoring, and the lack of an integration test that checks all boundaries at once (X1) into our action plan. I made the call on the last one myself.

> If not all conditions are met, we withhold the grade A claim.

We closed off, from the start, the path of claiming A on partial results.

## What This Choice Meant

The sealed set is **scored only once**. Failed, timed-out, and invalid runs also stay in the denominator. Three days later, these rules were actually put to the test. When the first sealed run was invalidated by a defect, and when the automatic grade A determination came back unmet because of a format error in the scoring input, the rules set on this day told us what we could and couldn't do.
