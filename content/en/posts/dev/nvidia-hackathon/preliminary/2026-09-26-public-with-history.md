+++
date = '2026-09-26T20:50:00+09:00'
draft = false
title = '[2026-09-26] Going Public with the History, Not a New Repository'
summary = "Instead of creating a new public repository for submission, we decided to make the repository we developed in public along with its PR and decision history. Before going public, we swept the entire remote mirror with a secrets scan."
tags = ['NVIDIA Hackathon']
aliases = ['/posts/dev/nvidia-hackathon/2026-09-26-public-with-history/']
+++

The submission required a public GitHub repository. We had to decide which repository to make public and how.

---

## Background: Going Public Can't Be Undone

On the first day of design, we had put off the decision to go public until the morning of the deadline, because switching to public can't be undone. We moved that up two days and decided on the evening of September 26. In the same message, I said four things.

1. We'll make further design changes once the work is finished.
2. The public repository will be the current remote repository.
3. Please lay out the submission procedure.
4. Please also put into the documents how to handle an always-on daemon in operation, and sending push notifications when an alert fires after a batch.

## Options

| Plan | Pros | Cons |
|---|---|---|
| A single commit in a new repository | The history isn't exposed | **The evidence links to PR and decision history are cut** |
| **Make the current repository public with its history** | Decision records, PRs, and review records all remain as evidence | The entire past history becomes public |

Most of this project's claims relied on evidence links: "this decision is in this record, this number is in this scoring output." Moving to a new repository would cut those links. So we chose to go public with the history.

## The Price: We Scanned the Entire History

Going public with the history also exposes anything that accidentally slipped into past commits. So we pulled the entire remote mirror (810 commits, 85 remote branches, 106 PR refs) and swept every added line plus PR bodies and comments with a secrets scan. There were 0 secrets, and the 14 hits that looked like keys were all harmless URLs, shell variables, and test placeholders. We also checked the terms of use for external data (two Korea Customs Service APIs, the HS codes under the Korea Open Government License (KOGL), BACI) on their original pages and wrote them into the README.

## Switching to Public

That night I instructed: "Fix the follow-ups too, test it once in a browser, and once the review is done, go all the way to switching it public." After meeting the three conditions (follow-up fixes, E2E test, independent review completed), we pulled the mirror again right before going public and re-scanned it. At 22:53, the orchestrator switched the repository to public with a command. We confirmed that the repository page, the README, and the submission form document opened without authentication.

## What This Choice Meant

The operational expansion plans (always-on daemon, alert push notifications) went in marked "plan only." They are plans that carry over the current invariants as they are (outbound traffic goes only to NIM, numbers come from verified values, notification wording is a review request rather than a verdict). The principle of **not writing about what we didn't build as if we had built it** applied just the same to the public documents.

After going public, merging became publishing. So we decided not to merge anything more until submission unless told otherwise.
