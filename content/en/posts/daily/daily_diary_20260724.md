+++
date = '2026-07-24T23:00:00+09:00'
draft = false
title = 'The Fate of the Candidate'
summary = "After the second brain's first successful conversation, I dove back into internal review. From unfiltered storage to LLM Wiki pitfalls to a Discord-based approval flow — a week of wrestling with the plan."
+++

*Written July 24, 2026*

After the first successful conversation in my second brain last weekend, I went back into internal review.

1. It's fine that the second brain stores data, but if it stores everything without any filtering, it's no different from the original second brain.
2. If an LLM exists inside to classify information and starts deciding whether to store things in the second brain before saving them, there's no human involvement — and the pitfalls of an LLM Wiki would emerge.
3. In other words, human involvement is necessary, but having the user open the program every time they want to save something highlights the original second brain's weakness.
4. Conclusion: Through Discord — the sole channel — get confirmation from the user on what to store, and decide whether to save or discard information that had been saved as a Candidate.

That was the gist of it, and when I reviewed it together with AI, sure enough, even information stored as Candidates was being retrieved and used. We decided to fix that.

---

On top of that, after re-reviewing with Codex and Claude, additional defects were found: first, there was no function to verify cited information when answering; second, there was no function to have the user re-review and Lint outdated memory information; and third, retrieval after data storage was using flat search instead of hierarchical lookup, posing a risk of performance issues once a lot of data accumulated.

I'd been reviewing this steadily for a week and had completed the plan by that point.

The first and third were just wiring issues or existing risks with low danger, but the second was a flaw in my design — born from wanting to keep the advantages of an LLM Wiki — so I felt a sense of urgency.

---

I'm currently working on improvements through Codex, and interestingly, there was actually a YouTube video covering a similar case.

In that video, they handled it by escalating the approval process itself — getting user approval before saving — and also escalating the Lint re-involvement request for outdated knowledge, so the system asks the user questions and receives answers. I benchmarked this and implemented something similar.

However, my second brain has a two-layer architecture of **Companion** and **Brain**, where Brain is the application that processes information.

Also, since this is a personal project, it doesn't run 24/7, and there will likely be situations where the internet is down and Discord can't be checked.

So, both Companion and Brain hold the full Discord conversation history and candidate knowledge (Candidates), but Companion only holds the ID values of the candidate knowledge along with information about how the user handled them.

Brain then asynchronously fetches that information, handles the promotion of each listed piece of knowledge, and reflects it in its own data — ultimately storing whether it was approved, rejected, or canceled for some reason.

Once Brain's processing is complete, it sends a signal to Companion to give the user relevant feedback. That's the direction of the changes.

Since this is the result of a week of wrestling with the plan, I hope the testing goes smoothly.

Please.
