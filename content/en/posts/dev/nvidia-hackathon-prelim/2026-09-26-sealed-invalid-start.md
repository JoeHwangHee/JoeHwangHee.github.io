+++
date = '2026-09-26T09:55:00+09:00'
draft = false
title = '[2026-09-26] We Declared the First Sealed Run Invalid'
summary = "The first sealed run, started right after the freeze, failed on all 15 cases. It was a run-blocking defect: 0 model calls and 0 sealed outputs produced. We treated the batch as an invalid start, added a sealed-run option, and started fresh."
tags = ['NVIDIA Hackathon']
aliases = ['/posts/dev/nvidia-hackathon/2026-09-26-sealed-invalid-start/', '/posts/dev/nvidia-hackathon/preliminary/2026-09-26-sealed-invalid-start/']
+++

Thirty minutes after freezing the rulebook, we started the official holdout40 (40 sealed synthetic cases) run in the sandbox dedicated to official scoring. Fifteen cases ended `FAILED` in a row. We stopped at 9:32.

---

## Background: The Cause Wasn't the Model

There were 0 model calls. The orchestrator found the cause without opening the sealed output contents, using only status-code counts, file names, and non-sealed diagnostic runs.

The data-set table of the case investigation command `run-case` didn't have holdout40. It was designed to accept real data only from the development real-data series, so sealed real data was rejected. **There was simply no path to run the sealed sets at all.** The runner hadn't done a dry run of the sealed sets through the actual dedicated sandbox, because a dry run would itself become the official run, and that gap turned straight into the incident.

## Options

There was the rule, "score only once." How should these 15 cases be treated?

| Plan | Judgment |
|---|---|
| Keep the 15 in the denominator and continue only the rest | Dropped — there's no resume feature, and a new image was needed anyway |
| Score as is | Dropped — every row failed to run, so it isn't an evaluation |
| Remove the sealed-real-data rejection without an option | Dropped — loses the protection against accidentally running sealed data during development |
| An environment variable or a new subcommand | Dropped — broadens the scope of changes to shared agreements |
| **Treat as an invalid start + `run-case --sealed`** | **Adopted** |

My answer was, "Both as recommended."

## Why This Doesn't Break "Score Only Once"

The purpose of that rule is to prevent rerunning after seeing the results. This failure wasn't a result from the model or an agent but a **run-blocking defect**, and not a single piece of sealed content was produced. There were no results to see, so this wasn't rerunning after seeing results. We interpreted it this way and recorded the interpretation and the course of events in a decision record. The frozen items (rulebook, scorer, judgment policy, validator) weren't changed (diff 0).

## What This Choice Meant

`--sealed` is a single option with no value. Only when this option is present does it accept the sealed sets. We rebuilt the image with the fix commit, confirmed that all violation tests matched predictions, and at 10:10 started both sealed sets fresh.

## Lesson

**Rehearse the sealed-set runner with non-sealed data first.** If the structure is such that a dry run becomes the official run, you have to run the same path end to end once with different data. This lesson went into the evaluation procedure of the finals guide.
