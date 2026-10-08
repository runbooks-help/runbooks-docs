---
title: Why runbooks get less wrong
slug: why
order: 2
layout: sections
description: Who Runbooks is for, and why the runbook you write today will be wrong tomorrow.
---

Most operational tooling assumes a calm, daytime engineer. Runbooks is for the one who gets paged.

## Built for the worst moment

You are half-asleep, panicking, and under time pressure. A page has just woken you. You may not have written this runbook, and you may not own the system. So we hold one bar: **make the next step obvious without a wall of text.**

Every page stays quiet: collapsible steps, a dark low-glare palette, one clear next action, no marketing voice inside a procedure. Anything competing for attention spends it, and at 03:00 you have little to spend. Write for that reader and you write for yourself.

## The runbook is always wrong

You write your runbook wrong the first time. The system moves, you never hit the edge case, you guessed. It stays wrong, and most tools leave it there.

The real question is *how fast does it get "less wrong", and can it self-correct?*

## The loop

Execution is the source of truth, so keep what happened:

- **Capture.** Record notes during the incident, especially when the runbook didn't help.
- **Review.** A reviewer (often an agent) reads the runbook against those notes and suggests classified changes: a step to fix, a lookalike to add, an environment fact to generalise, or a note that is not the runbook's job at all.
- **Revision.** You decide and edit. The reviewer never rewrites a procedure on its own.

## Symptoms and red herrings as first-class citizens

You arrive with a symptom, so lead with it ("Replication Lag (MTS Deadlock)", not "MTS Deadlock Recovery").

A symptom-first page also needs its false leads. A **lookalike** ("this also looks like X, but it isn't") is content worth writing down: tell the next responder what it *wasn't*, and save them the twenty minutes you just spent.

## The right tools

We can't force you to write a good runbook, and we don't try. We give you the tools that make the good path the easy one.

## Where next

- [Install & quickstart](install.md)
- [Writing a runbook](../guides/writing-runbooks.md)
