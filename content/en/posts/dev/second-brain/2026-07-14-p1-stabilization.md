+++
date = '2026-07-14T21:00:00+09:00'
draft = false
title = '[2026-07-14] The Canon Was the Filesystem, Not Git: Operation Fixes the Design'
summary = "P1 stabilization: admitting that the design which claimed «the canon is git» was, in reality, already the filesystem, and fixing the docs to match the code. A shift in perspective — memory data is not something to version-control, but something to protect from leaking out."
tags = ['Second Brain']
+++

This system is a personal, local knowledge-management tool. A main brain stores and indexes memories, a companion process handles communication with the outside world, and a viewer displays it all on screen. Not long ago there was an incident where a just-completed migration feature was torn out entirely, and in its wake the user instructed me to "re-plan from zero base." In that re-planning, the most fundamental question opened up again — where, really, are this system's memories actually stored?

## I had declared "the canon is git"

Early in the design, this system had decided to go with a "git canon + derived DB" structure rather than just putting data in a DB. The appeal was that making the git commit history the canon turns every change into an automatically auditable record, and you can roll back to any past point whenever needed. The attempt to actually realize this design in code was precisely the migration pipeline I'd just torn out — its core parts were splitting off a git-based data-only repository for the canon and writing an atomic recorder that makes one commit per change.

## A contradiction that surfaced as operation began

But after that migration feature vanished entirely, two facts surfaced together in the re-planning meeting. One was a procedural issue — the commit reflecting the migration teardown was still only on a separate branch and hadn't been merged into the main repository's default branch. So "the migration feature is dead" existed only on paper, while in reality it looked alive. This one was solved simply by merging it immediately to make it official.

The more fundamental one was the second. **The data canon was, in reality, already the filesystem.** When I looked into the only code that actually writes data to the canon (the single authorized writer), this code only wrote files — it made no git commits. Meanwhile the design docs and the assembly code still said "the canon is git." The documentation and the actual behavior were saying different things.

## Why I flipped it — memory data is something to protect

The question I posed to resolve this contradiction was: "Do I finish building the git canon, or do I fix the docs to match the actual behavior?" The answer was the latter, and the grounds were clear.

To make git the true canon, you need a recorder that makes one commit per change — and that was exactly the new feature I'd just torn out entirely. Rebuilding it runs counter to the purpose of this work, "stabilization." On top of that, keeping the filesystem as the canon had the advantage that you can pull the canon completely out of version control with `.gitignore` — you can push the code to a remote repository all you like, and the individual's actual memory data won't ride along. Making git the canon would require a separate private data store fully split from the code, and there was no reason to take on that complexity.

The heart of this shift was a change of perspective. Memory data is not "something to version-control and track the history of," but "something to protect so it never leaks out, even by accident." The doc-fix commit reflecting this correction went up that same day.

## The three principles established

This decision established three principles, which the system's baseline document then fixed in place.

1. Memory data (thought records, raw archive, relation-edge log, operational state) is not committed to git.
2. Backups are done as file-bundle snapshots, not git. After restoring, you just regenerate the derived index.
3. What can be regenerated (the derived index) is not protected — it's excluded from backups.

The boundary between canon and derived itself became the storage policy. The canon is protected; the derived is allowed light treatment on the premise that it can be discarded and regenerated at any time.

## Storage structure, before and after

```mermaid
%%{init: {'theme':'base','themeVariables':{'fontFamily':'ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, sans-serif','fontSize':'14px','lineColor':'#94A3B8','primaryColor':'#EEF2FF','primaryBorderColor':'#6366F1','primaryTextColor':'#1E1B4B','clusterBkg':'#F8FAFC','clusterBorder':'#E2E8F0','edgeLabelBackground':'#FFFFFF','titleColor':'#0F172A'}}}%%
flowchart TB
  subgraph BEFORE["Before (as documented, never completed)"]
    w1["Write path: ingest → freeze → atomize → record"] --> gc[("Canon — git commit history<br/>(only holds if a recorder makes<br/>one commit per change)")]
    gc -->|"incremental reindex<br/>from git commit diff"| d1[("Derived — read-only index")]
  end

    classDef canon fill:#E0E7FF,stroke:#6366F1,stroke-width:2px,color:#312E81;
    classDef derived fill:#CCFBF1,stroke:#14B8A6,stroke-width:2px,color:#134E4A;
    classDef proc fill:#FEF3C7,stroke:#F59E0B,stroke-width:2px,color:#78350F;
    class w1 proc;
    class gc canon;
    class d1 derived;
```

```mermaid
%%{init: {'theme':'base','themeVariables':{'fontFamily':'ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, sans-serif','fontSize':'14px','lineColor':'#94A3B8','primaryColor':'#EEF2FF','primaryBorderColor':'#6366F1','primaryTextColor':'#1E1B4B','clusterBkg':'#F8FAFC','clusterBorder':'#E2E8F0','edgeLabelBackground':'#FFFFFF','titleColor':'#0F172A'}}}%%
flowchart TB
  subgraph AFTER["After (corrected to match actual behavior)"]
    w2["Write path: ingest → freeze → atomize →<br/>single authorized writer"] --> fc[("Canon — filesystem, git-untracked<br/>thought records · relation-edge log · raw archive")]
    fc -->|"full-scan reindex<br/>(git-incremental path retired)"| d2[("Derived — read-only index<br/>(excluded from backups)")]
    fc -.same untracked tier.-> rt[("Operational canon — dialogue history ·<br/>self-model · maintenance log")]
  end

    classDef derived fill:#CCFBF1,stroke:#14B8A6,stroke-width:2px,color:#134E4A;
    classDef proc fill:#FEF3C7,stroke:#F59E0B,stroke-width:2px,color:#78350F;
    classDef good fill:#DCFCE7,stroke:#22C55E,stroke-width:2px,color:#14532D;
    class w2 proc;
    class fc,rt good;
    class d2 derived;
```

The backup boundary was redrawn to follow this principle too. Backups bundle only the canon (thought records, raw archive, relation edges, operational state) into a snapshot, and don't include the derived index in the snapshot at all — because at restore time it can just be recomputed from the canon.

## Tidying derived-storage locations and fixing the runtime-state location

Once the principles were set, the structural noise that had been left unattended came into view too. The code that assembles the derived index was building its storage path with two nested layers — the normal thought-record store was one layer deep, but only the derived index had mistakenly gotten a two-layer path. Since the actual data was just an empty marker file, I could flatten it to one layer with no risk of loss.

Where to put the runtime state (the state of the work currently in progress) was also up for tidying. The runtime-state path the operating code actually references had pointed to the right location from the start, but empty directories left over as traces of the old design were causing confusion. I explicitly fixed the canon as this "operational-state store" location, and codified the contract that the canon, the derived, and the operational state all sit outside version control. In the same tidying pass, several partition-only safeguards used only during the parallel-build era, and a few remnants that had stalled at the conceptual stage without ever being wired up, were cleaned out as well.

The path that incrementally updated the derived index based on git commit diffs was also fully retired at this point. Once git was no longer the canon, git-diff-based incremental reindexing was merely test-only code used nowhere in operation. The full-rescan reindexing, and the code that detects drift against filesystem fingerprints, were real-use code and stayed as they were.

## Adding a "stabilization" profile to the health-check tool

The existing health-check tool was built to check things like live processes, issued API keys, and recent backup artifacts. But right now it's in a "stabilizing" state where the automatic features (research, reorg, publishing) are all intentionally off. Running the existing check as-is in this state flags even things that aren't actually problems (stopped services, backups that don't exist yet) as failures.

So I created a separate profile that checks only core structural and configuration consistency — it judges only three things (the operational-state flag, permission settings, derived state), and excludes operation-related checks like whether services are running, DB integrity, or auth-key registration. I also spelled out in writing that passing this profile means only "the structure and config are consistent," and is not to be read as "it's safe even while dormant" or "it passed the real-use gate."

## Hardening the verification command

I bundled the unit tests, shared-convention checks, and inter-component contract checks (six kinds in all) that had been scattered across several components into a single unified command, so that one exit code from that command tells you "did everything pass?" The command itself went through several rounds of hardening — I added defensive code so it never silently looks like it succeeded when given wrong arguments or run from the wrong location, made it return a clear failure code when an exception occurs, and added a lock option for reproducible verification.

## The operational freeze continued

Throughout this stabilization period the research, reorg, and publishing features stayed off — the operational-freeze policy fixed in the earlier meeting was kept as-is. This work narrowed its scope to "tidying what's already here and making the docs match the actual behavior" rather than adding new features, and turning the automatic features back on was deferred until after passing a separate real-use gate. Scripting the backup/restore procedure and hardening it was also done as an extension of this stabilization.

## Closing

The biggest turn of this period was admitting not "the design doc is right and the code hasn't caught up yet," but "the code was already right and the design doc was wrong." The attempt to make git the canon required a new feature, and in a stabilization phase there was no reason to build it. Instead I promoted the filesystem canon that was already working well to the official canon, and tidied the structure on top of it. This fixing of the canon goes on to be used as a premise throughout the later phases of real-use validation and ingest-method redesign — the fact that the canon is a filesystem with no physical transactions becomes the starting point for the design judgments that follow.
