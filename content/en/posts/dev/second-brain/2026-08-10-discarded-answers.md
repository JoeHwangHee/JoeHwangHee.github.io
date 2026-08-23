+++
date = '2026-08-10T23:00:00+09:00'
draft = false
title = '[2026-08-10] Declining a Backup — Twice'
summary = "Perfectly valid answers were being silently discarded because of format wrapping. We fixed it by reusing an existing utility, and declined a ledger backup twice — not out of carelessness, but to keep the scope tight."
tags = ['Second Brain']
+++

From August 8 through 10, we fixed a defect in Second Brain where answers were being silently discarded.

---

## What Happened

The Brain backend was generating valid answers, but they never reached the user. The cause: the LLM was wrapping its JSON response inside a Markdown code block. The parsing logic couldn't strip that wrapper, so it treated the response as "no valid answer" and threw it away.

## Three Decisions

**First, we reused an existing utility instead of writing a new one.** A shared utility for stripping Markdown wrappers was already in use across five places in the system. There was no reason to create new code.

**Second, we settled the stripping order.** "Try to parse as-is first; only strip the wrapper on failure." We actually tested the reverse order (always strip first) and found it broke valid JSON that contained code blocks. The order was confirmed through empirical testing.

**Third, format-deviation retries are limited to exactly one.** For ledger safety, the operation had to complete within a single call. Unlimited retries would make the ledger state unpredictable.

## Why We Declined the Backup — Twice

During the work, a ledger backup (21 KB) was proposed twice. Both times, we decided it was **"out of scope."**

This wasn't dismissing safety — it was holding the scope. The goal of this task was "rescue discarded answers," not "build a ledger protection system." Including the backup would widen the scope, and a wider scope delays completion. We stuck to the principle: **solve one problem at a time.**

## Reply Classification

We separated the failure reply message to the user into three distinct types:

| Situation | Reply |
|---|---|
| No relevant material found | "I couldn't find any related memories" |
| Format error | "A format issue occurred while generating the answer" |
| Processing incomplete | "I was unable to complete the processing" |

Previously, all three cases produced the same message. Separating them lets the user tell what kind of failure occurred.
