+++
date = '2026-07-26T15:30:00+09:00'
draft = false
title = 'Have You Heard of Graph Engineering?'
summary = "The evolution of AI agent design — from single loops to graph engineering. Looking at the direction through the Auto Company project: injecting human work structures into AI."
+++

*Written July 26, 2026*

Have you ever heard of the concept of graph engineering?

I first came across it simply through an image posted in a KakaoTalk group chat I'm in.

It's the image below — you can see it's structured to explain the evolution of engineering so far and the concept of graph engineering.

![Evolution of engineering and the concept of graph engineering](/images/daily_diary_20260726/image.png)

And below is the result of asking Gemini about the concept.

## Graph Engineering in AI Agent Design

In the field of AI agent development, **graph engineering** is a methodology for designing and controlling the workflows and dependencies of multiple AI agents in a "graph" form with nodes and edges.[^1]

In the past, a single repetitive loop structure (Loop Engineering) was mainly used, where a single AI evaluates, corrects, and achieves goals on its own. However, as tasks grew more complex, there were limitations — falling into infinite loops or becoming vulnerable to hallucinations. To address this, graph engineering, which separates agent roles and connects them in a network structure, is becoming the standard.[^2]

### Core Components

- **Node:** An independent unit that performs work. It can be an AI agent with a specific role, an external tool (API) call, or a human-in-the-loop step.[^3]
- **Edge and conditional branching:** Defines the execution order and conditional logic between nodes. For example, it explicitly controls complex flows like "when agents A and B finish working in parallel, C aggregates" or "if the review result is below standard, send it back to A (Cyclic Workflow)."[^3]
- **State:** Shared memory that maintains the full message history and execution context as it passes through the graph. This allows agents to perform long-term tasks without losing previous context.[^4]

### Representative Framework

A representative tool for implementing such graph-based orchestration is **LangGraph**. Developed by LangChain, this framework supports state persistence and checkpoints, enabling you to build complex multi-agent systems as stable, programmable graphs.[^4]

---
**References**

[^1]: [Graph Engineering Learning Pathway - explainx.ai](https://explainx.ai/pathways/graph-engineering)
[^2]: [Graph Engineering for AI Agents: Beyond Single Feedback Loops - Eigent AI](https://www.eigent.ai/blog/graph-engineering-ai-agents)
[^3]: [LangGraph: Agent Orchestration Framework for Reliable AI Agents - LangChain](https://www.langchain.com/langgraph)
[^4]: [Building Agentic Workflows in Python with LangGraph - Machine Learning Mastery (2026.07.20)](https://machinelearningmastery.com/building-agentic-workflows-in-python-with-langgraph/)

---

And below is the github address of a project called Auto Company that once created a buzz on git.

https://github.com/MaxMiksa/Auto-Company?utm_source=pytorchkr&ref=pytorchkr

If I understand correctly, the concept of Auto Company is essentially a process that uses LLMs to run sub-agents, each carrying out tasks according to their roles.

---

Ultimately, I think these two concepts are heading in the same direction.

First, you structure how humans work and inject it into AI.
Second, AI reviews its own output and networks with each other to minimize human involvement as much as possible.
Third, AI compensates for its context loss with databases.

These attempts actually seem to be heading in the same direction as all attempts using AI.

As automating workflows with AI gained attention, people have been trying to minimize human involvement however they can, and I see the continued evolution of this as the result shown in the image at the top.

There will probably be even more advanced structures beyond this in the future.

I just hope that when this automation enters the industry, it doesn't take my job too.
