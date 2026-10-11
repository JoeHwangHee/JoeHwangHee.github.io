+++
date = '2026-09-27T17:30:00+09:00'
draft = false
title = "[2026-09-27] Don't Move the Folders; Just Add a Mapping Table"
summary = "I asked whether we could restructure the directories to match the OpenClaw workspace structure from the training course. Since the submission, the frozen scorer, and the evidence all pointed to the current paths, we didn't move any files and only added a mapping table to the README."
tags = ['NVIDIA Hackathon']
+++

On the afternoon of the day we finished submitting, I was looking at the OpenClaw workspace structure diagram in the training course materials and asked, "Can we change the directory structure to match this image?"

---

## Background: A Structure Judges Are Familiar With

Since the training mission was OpenClaw and NemoClaw, judges would be familiar with that workspace structure (the slots for agent definitions, skills, memory, and so on). I thought that if we changed our repository to that structure, it would be readable at a glance.

## The Problem: Things That Point to the Current Paths

The orchestrator pointed out what would break if we moved things.

- **The long-form fields of the application we had already submitted** and the service files reference the current paths.
- **The frozen scorer outputs and the evidence** point to each other via committed paths. Frozen items can't be changed.
- Some 90 decision records and the PR bodies cite the current paths.
- If we significantly changed the repository during the judging period, the contents at submission time and at judging time would differ.

## Options

| Plan | Judgment |
|---|---|
| Move the directories to match the diagram | Dropped — breaks the paths of the submission, the frozen scorer, and the evidence |
| Leave it as is | Dropped — hard for judges to map the structure |
| **Don't move anything; add only a mapping table to the README** | **Adopted** |

My choice was "don't move anything; add only a mapping table."

## What We Chose: A Mapping Table

In section 3 of the README, we added a table showing where each slot of the OpenClaw workspace (the set of files an agent reads as its execution context) corresponds to in our repository. Laying it out, the only slot we filled was a single skill. In the demo, the OpenClaw agent went through this skill to call the CLI inside the sandbox. For the remaining slots, like `SOUL.md`, `TOOLS.md`, `USER.md`, `memory/`, and `HEARTBEAT.md`, we wrote plainly that "this repository didn't create them." We added the reason too: it's a demo that uses a fixed snapshot, so there are no periodic tasks. We also made clear that the `AGENTS.md` at the top of the repository isn't the workspace one but a guide for Codex. We added an explanation that the static layer fixed when the sandbox is created (filesystem rules and so on) is determined by the NemoClaw version, so we didn't commit it. This decision was also recorded as a decision record.

## What This Choice Meant

Instead of changing the structure, we **provided a translation**. Honestly leaving the slots we didn't build as blanks follows the same principle. The paths of the evidence are a chain that holds up the project's credibility, and we couldn't break that chain just to make things look nice. A mapping table was the cheapest way to show the same content in two structures.

## Afterward

This was the last merge to the preliminary-round repository. After that, we didn't change the repository during the judging period.
