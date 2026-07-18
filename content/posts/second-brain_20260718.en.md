+++
date = '2026-07-18T20:36:00+09:00'
draft = false
title = 'Second Brain, LLM Wiki, and the Hybrid'
tags = ['Second Brain']
+++

Riding the recent development boom powered by AI, there's one keyword that has drawn attention with the strongest impact.

It's the **Second Brain**.

At first hearing, it's a term compared against the LLM Wiki that Andrej Karpathy talked about. Here's how Google's Gemini described the difference between the two when I asked:

> Second Brain is a personal knowledge management (PKM) methodology in which a person proactively records and manages information, while an LLM Wiki is an automated three-tier (source–wiki–schema) knowledge base in which, on top of the Second Brain's data structure, an AI (an LLM agent) autonomously summarizes and structures knowledge and continuously accumulates it.

I'll write up the detailed findings in a separate post. (Since this is data I had Claude research and compare, there may be a certain degree of bias.)

In brief: the original Second Brain is Forte's pre-AI-era methodology, a system where a person selects and captures material themselves, organizes it into PARA (Projects in progress / Areas of ongoing responsibility / Resources / Archives), and progressively compresses it on each re-reading to build knowledge material to draw on later for creation and action; the LLM Wiki, on the other hand, is a system where an AI takes the source text a person feeds it, compiles and maintains it into a structure the AI finds easy to handle (e.g., md files) in an immutable state (including checks for contradictions and stale information), and when asked a question, reads that to answer with derived knowledge and reuses that answer.

The original Second Brain is a method carried over from forms closer to paper cards and scrapbooks, and it has the trait of the person managing it steadily, since selecting, classifying, organizing, and searching are all done by the person. But watching the recently popular Second Brain videos, I saw many processing approaches that hand the management over to tools like Obsidian, or adopt the RAG method to embed each piece of information for storage and assembly — and this looks like the **'hybrid'** approach of the LLM Wiki, where the selecting, classifying, organizing, and searching of information are entrusted to an AI or a system.

---

So then, what are the pros and cons of the two approaches?

For the original Second Brain, I think the keyword is **organizing it yourself**. That a person (an individual) organizes it means their own subjectivity enters the gathered information — the more you gather, the more the direction of the information tends to resemble your own judgment. The downside is also that you organize it yourself. In an age when hundreds of pieces of information pour in every single day, having an individual gather all of it is paradoxical. And organized to your own taste, at that? If I had time for that, I'd rather run a few more Google searches or ask an LLM to search instead.

What about the LLM Wiki? First, the advantage of this system as stated on paper is the part where **the 'AI' does 'everything' 'on its own'**. Once I just set the criteria, it doesn't merely process the information — it even maintains any possibly outdated information or leftover junk on its own. It feels like some kind of master key, and the critical thought "can this really be possible?" comes to me first; but if I take it as established and think only about the pros and cons, the advantage would be that maintenance requires little human effort. And I think the downside is the same. A personal knowledge repository that takes no human hands — I can't tell how far my own thoughts even went into it, and while some degree of judgment ought to happen when processing information, if you get too used to this method, at some point a person becomes a being that just "clicks," and the AI seems to do "everything" "on its own." (The ending of a certain SF film comes to mind, but I'll stop my thoughts here.)

---

In any case, I too am developing a hybrid-style personal knowledge repository that fuses the LLM Wiki's system into this kind of Second Brain.

The first thing I built was a repository storing things directly in the filesystem, so that portability was sufficiently guaranteed just by moving the folder as-is — but the conclusion was that it's actually hard to use without external intervention by an LLM. Because I had it save into md files and made it check the index one by one, and using that one by one requires too much intervention by a "person."

So, taking that as a lesson, what I'm developing now is the Second Brain I'm currently building.

Going forward, in posts tagged Second Brain, I plan to update the development status of the Second Brain little by little.
