+++
date = '2026-10-07T11:45:00+09:00'
draft = false
title = '[2026-10-07] Reading the Mission: Routes with Evidence Grades for K-Content Visitors'
summary = "Given a mission to build an agent that judges Korean cultural, historical, and travel context, we set our direction: an agent that tells visitors who came after watching K-content a route through the story locations and the origins of place names, along with evidence grades."
tags = ['NVIDIA Hackathon']
aliases = ['/posts/dev/nvidia-hackathon/2026-10-07-finals-direction/']
+++

At 10:30, the mission was revealed through the onboarding materials. We had to set a direction in just over an hour.

---

## The Mission

> Korea's culture, history, regions, and travel carry context that is hard to grasp from search results alone. Build an AI Agent that understands multiple sources and the user's situation, judges what information is needed, and connects it to a more accurate and meaningful experience.

Attached to this was a common OpenShell test. The agent processes requests using the materials in `/hackathon/input` and writes results to `/hackathon/output`. `/hackathon/restricted` and `/hackathon/secrets` are off-limits. The test materials mix in irrelevant information, outdated material, and conflicting information.

## Retrieval Structure: The Decision Changed Twice

The first implementation started with a lookup and indexing structure using local embeddings. On this part, the decision went back and forth.

| Time | Decision | Reason |
|---|---|---|
| 10:3x | Hybrid (morphological BM25 + dense embeddings) | For Korean history and culture material full of proper nouns, BM25 catches them; embeddings catch rephrased questions |
| 10:5x | BM25 only | I instructed "use BM for the embedder," and after confirming BM meant BM25, we removed dense embeddings |
| 11:0x | **Back to hybrid** | The design conversation we had shared in advance recommended hybrid, and we set that conversation as the highest authority |

The decision went back and forth because the order of authority hadn't been set. So we spelled it out. **The shared design conversation → the team lead's instructions → the documents prepared in advance.**

We also sorted out the word "training." It isn't fine-tuning that changes weights; it's **indexing**. Fine-tuning and evaluating a model within one day wasn't realistic.

## The Common Test Materials Only for the Final Test

The organizers' common test materials were test data with traps built in. Developing against them would invalidate the evaluation. We decided to do testing and indexing during development with separately gathered material, and use the common test materials only for the final test. Files that could serve as answers were kept out of the development agents' view.

## Direction Set

At 11:45, we locked in the direction.

> An agent that, when a visitor who came after watching K-content states a goal, builds a route connecting the story locations and tells them the origin of each place name along with an evidence grade.

The demo line was, "I came after watching KPop Demon Hunters. I have three hours this afternoon, starting from Hyehwa." The agent interprets the goal, picks places from a theme pack, looks up station names and their origins, sorts out the names in the work, the old names, and the current names, attaches evidence grades, and saves the route file. Finally, when it requests posting, it gets blocked, and only after approval does it go through.

## What This Choice Meant

The key words of the mission were "context" and "reliability." So we chose to show not so much the content of the answer as **how trustworthy the answer's evidence is**. The origins of names are often written differently from source to source. Not hiding those differences but showing them as grades became what set this agent apart. The principle from the preliminary round, "numbers from verified values," carried over into the finals as "evidence grades on claims."
