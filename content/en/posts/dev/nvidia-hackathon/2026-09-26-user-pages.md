+++
date = '2026-09-26T18:28:00+09:00'
draft = false
title = '[2026-09-26] From an Admin Screen to Staff Screens'
summary = "Looking at the screen we'd built with my own eyes, I pointed out that it was for system administrators and actual staff couldn't use it. We built three new staff screens, starting from the alert list, plus an English version."
tags = ['NVIDIA Hackathon']
+++

In the evening, I opened Screen 1, built under Scenario C, myself. And I pointed out two things.

---

## Background: The Screen Didn't Match What the Tool Is

1. This isn't a tool where the user enters something. It's **a tool that takes statistics as snapshots and runs detection**. So the screen should start from the alert list.
2. The current screen shows snapshot IDs, run folders, policy names, and hashes as they are; it's **for system administrators**. Staff who don't know the internal sources can't use it.

The orchestrator put three screens on a private design canvas: the staff home (a monthly alert list), case review (a plain-language explanation), and a support screen (decision record, re-investigation, help). I replied: "Overseas companies will be checking this, so add an English version, and go with this design."

## Options

| Plan | Judgment |
|---|---|
| Submit with Screen 1 only (Scenario C as is) | Dropped — a screen staff can't use can't show practicality |
| Make the support screen a Q&A chat | Not proposed — needs new model calls and instructions, risking the freeze and cost |
| **3 staff screens + English version, keep the admin screen** | **Adopted** |

We didn't touch the scoring path. In implementing the decision record as a mock approval unit, part of the mock approval that had been cut from scope that morning came back. Since the number of screens changed, we also revised the claim wording in the submission to "3 for staff + 1 for administrators." That was a shared agreement too, so I approved it.

## v2: Only What's Needed

After looking at the implemented screens, I gave feedback again. Remove meaningless elements, separate the card and filter areas from the data area, don't show internal variable names in columns, and show a full-screen loading state while running. After seeing the v2 canvas, I specified six things, from column names, card width, and button placement to a "Back to list" in the loading window and an "Investigating" indicator on every screen.

From the staff screens, we removed item codes, snapshot IDs, run folders, policy names, hashes, and raw status codes entirely. The only exception is a single line: "Case number for inquiries." We built the loading window as a fixed-position container with a background subprocess instead of a dialog. This is because Streamlit reruns the script on every button press, which cuts off synchronous execution.

## A Change in the Review Path

At the same time, Codex hit its usage limit (until October 23, per the notice). Under the operating rules, merging without a Codex verdict needed my approval. The orchestrator requested a fallback path: merge on the Claude independent reviewer's PASS and get the Codex re-review after the limit lifted. I approved it with "Merge the PR."

## What This Choice Meant

No matter how good the evaluation numbers are, practicality isn't proven if staff can't open it and use it. At the end, we ran an E2E test that clicked through the entire user flow with browser automation and passed all 16 items (keyless replay runs only). In the early morning of September 27, we ran an actual NIM investigation on one real-data alert from the staff screen and confirmed that all 5 model requests returned 200 and the 7 numbers in the report matched on cross-check.
