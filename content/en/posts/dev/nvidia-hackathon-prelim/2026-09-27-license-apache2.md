+++
date = '2026-09-27T02:15:00+09:00'
draft = false
title = "[2026-09-27] Public, but No Right to Use It: Apache-2.0"
summary = "After making the repository public, I asked what a license actually says. Hearing that without a license file there are no reuse rights even when the code is public, we chose Apache-2.0."
tags = ['NVIDIA Hackathon']
aliases = ['/posts/dev/nvidia-hackathon/2026-09-27-license-apache2/', '/posts/dev/nvidia-hackathon/preliminary/2026-09-27-license-apache2/']
+++

Right before switching to public, the orchestrator said we needed to decide on a license. I asked, "What does a license actually mean?"

---

## Background: Public Is Not the Same as Permitted

The explanation went like this. Even if a repository is public, **without a LICENSE file it grants no one else the right to reuse, modify, or redistribute it.** Only viewing and forking within GitHub are possible. We were submitting a public repository while leaving the answer to "can I take this and use it?" blank.

## Options

| Plan | Judgment |
|---|---|
| No license | Dropped — it can only be viewed, with no right to use it |
| A short permissive license like MIT | Possible, but has no patent clause |
| **Apache-2.0** | **Adopted** — permissive, with a patent clause |

The orchestrator recommended Apache-2.0. It's permissive, has a patent clause, and also matches the notation of NVIDIA's official skill ecosystem (an inference). I answered, "I'll go with the recommended license."

## Scope of Application

We put the full text of the Apache License 2.0 at the root and wrote the scope into the README. It **applies only to the code and documents this project created**, and third-party data such as Korea Customs Service statistics or BACI follows each source's terms. We also re-rendered the governance cards of the three skills with the official generator to align the license notation.

## Two Checks the Same Early Morning

- **A real NIM investigation**: I told the orchestrator to actually press "Start investigation" on the staff screen. One real-data alert finished in 13.1 seconds, with all 5 model requests at HTTP 200, 7 report numbers matching on cross-check, and 0 traces of the key.
- **Training mission requirement**: I asked again whether completing the course was mandatory. The official page's original text was "Review the training mission," and there was no wording requiring completion or collecting proof anywhere on the official page, the application form, or the help pages. The earlier explanation that "it must be taken by September 27" was an error that stated the roadmap's internal schedule as if it were an official deadline, and it was corrected.

## What This Choice Meant

Making it public is "showing it," and a license is "letting people use it." Even for a hackathon result, the meaning of making it public is only complete when others can take it and use it. That same early morning, we also created a planning and development log that captured this project's development order and the reasons behind its choices in one document. It's the document this blog series is based on.
