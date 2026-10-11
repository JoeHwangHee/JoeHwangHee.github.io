+++
date = '2026-10-07T16:50:00+09:00'
draft = false
title = "[2026-10-07] The Public Release: Material with Unknown Terms of Use Doesn't Go Public"
summary = "Building the public repository for submission, we left out material whose terms of use we couldn't confirm, and included the development documents so judges could check them. With the deadline looming, this time we ran the independent review after merging."
tags = ['NVIDIA Hackathon']
aliases = ['/posts/dev/nvidia-hackathon/2026-10-07-finals-public-release/']
+++

After the feature freeze (15:50) and the evaluation, we spent the remaining hour on the public repository for submission. This decision took shape over three rounds.

---

## Round 1 (14:3x): Only What's Needed, in a New Repository

At first we went the opposite way from the preliminary round. Instead of making the development repository public as is, we decided to move **only what was needed into a new repository in a single commit**. The finals repository had a day's worth of internal records and teammates' working branches mixed in.

The reason we had chosen to go public with the history in the preliminary round (evidence links) still held. But the finals were a single day, and the risk of exposing unorganized internal records outweighed the value of the history.

## Round 2 (16:3x–16:5x): Material to Leave Out

Preparing the public version, we looked again at the terms of use of our material.

| Material | Handling | Reason |
|---|---|---|
| Documents quoting news articles | Excluded from the public version | We couldn't confirm the terms of use |
| Travel-time and research material sourced from Seoul city media articles | Excluded from the public version | Same reason |
| Teammates' working branches with unorganized text still in them | Not moved | The new repository doesn't carry over history |
| KOGL Type 1 public data (station name origins, etc.) | Included | The attribution conditions are clear |

There was one rule. **Material whose terms of use we couldn't confirm doesn't go public.** Going public can't be undone, so we kept what went public narrow. As a result, the public version's knowledge index shrank to 399 documents, and 5 places in the theme pack lost their evidence of appearing in the works. We wrote this into the README as is, along with the note that the evaluation results were measured on the full data.

## Round 3 (16:5x): The Posting Repository as the Submission Repository

At the end, we changed direction once more. We had the dedicated repository used for the posting demo (already public) double as the submission repository, and decided to **include the development documents as well**. What I said was: "It's fine to include the development docs. Let's make it so the judges can check with them." Judges needed to be able to follow the evidence paths in the verification guide. We added commits on top of the existing history and didn't force push.

We removed one thing from the README: the demand statement written on the "Problem" line. **There was no separate evidence for the demand.** Removing unsupported claims had been the principle since the preliminary round.

## An Exception Before the Deadline

At 17:00, the deadline was looming. The usual rule was "merge after review," but this time we switched the order to **independent review after merging**. We judged it better than missing the deadline waiting for review. The track content had already been reviewed twice each within the tracks, and what we added at the end was documents and on-screen wording. The review found 5 document mismatches, and we fixed all of them and pushed again.

We also wrote the evaluation results honestly. The main system scored 8/8 on development cases and 3/4 on holdout, while the baseline (Nemotron alone) scored 3/5 and 1/3 respectively. **The samples were small and the intervals overlapped, so we didn't claim a difference.** We also disclosed that these were synthetic cases.

## What This Choice Meant

Even for the same "go public" decision, the preliminary round and the finals had different answers. In the preliminary round, the evidence links built up over six days were the value; in the finals, the terms of use of material mixed together in a single day were the risk. The principle was the same (make the evidence visible, keep what we couldn't confirm private); the situation changed the answer.

Around 17:20, we submitted via Slack.
