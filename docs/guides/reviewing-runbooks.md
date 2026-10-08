---
title: Reviewing a runbook
slug: reviewing-runbooks
order: 2
layout: sections
description: Close the loop — turn the notes captured during an incident into a classified review, then a revision.
---

A runbook is wrong the first time you write it. The fix is a loop: capture what happened, review it against the page, revise. This page walks one real run through the whole loop.

The example is `replication-lag` from the gallery: a procedure for an MTS deadlock, three runs, and the review that came out of them.

## Capture: what the reader leaves behind

The reader leaves notes during the run. The notes panel records them against the page, and each run syncs an immutable snapshot (`runbook.md`, `notes.md`, images). Ask for what matters: what helped, what wasted time, and what looked like the answer but wasn't.

Three snapshots came back from this runbook:

````markdown [Notes — replication-lag, three runs]
# 2026-10-04 — ran it top to bottom

Worked as written. Lag drained about 40s after the kill.
(also: the alert paged the old payments rota — not this page's job)

# 2026-10-05 — wasted ten minutes

The symptom looked exactly like the MTS deadlock, but there was no
`waiting for handler commit` in `SHOW ENGINE INNODB STATUS`. It was a GTID gap:
`Retrieved_Gtid_Set` far ahead of `Executed_Gtid_Set`.

Also: `KILL {{PROCESSLIST_ID}}` is ambiguous under pressure. The processlist
showed two suspects, and we killed the wrong one first.

# 2026-10-06 — second run

Confirms the kill step: same ambiguity, we now know to pick the handler-commit
worker, but the page should still say it. Our replica is `db-2.prod.internal`,
and we run from the bastion — the `mysql -e` lines assume you're already on the
host.
````

## Review: run the reviewer

A reviewer reads the runbook and those notes and proposes classified changes. Runbooks ships the reviewer as an agent skill, served at [`/skill/runbook-review.md`](/skill/runbook-review.md). Install it at `.agents/skills/runbook-review/SKILL.md` and your agent picks it up. The [Agent access](../operations/agent-access.md) page covers the read surface it joins.

Every note lands in exactly one class, and the class decides its route:

| Class | Route |
|---|---|
| **Runbook fix** | A before → after edit anchored to a step. |
| **Lookalike** | A `> [!lookalike]` block: what else it looks like, and how to rule it out. |
| **Environment fact** | True for your deployment, not the shared page — proposed as a `var` or hint, always labelled. |
| **Out of scope** | Not the runbook's job — named and routed out, never absorbed into the page. |
| **No action** | Worked as written. No edit. |

The reviewer proposes; you decide. It never rewrites the page on its own, and it can propose nothing at all. Against these three runs it produced:

````markdown [Review — replication-lag]
# Review: Replication Lag (MTS Deadlock) — replication-lag

Snapshots: 2026-10-04T0312Z, 2026-10-05T2210Z, 2026-10-06T0904Z

## Free the blocked worker — say which worker to kill

class: runbook fix
evidence:
- "we killed the dependent-transaction one first — wrong" (2026-10-05)
- "same ambiguity, the page should still say it" (2026-10-06)
confidence: medium (2 notes, 2 snapshots)
replace:

    KILL {{PROCESSLIST_ID}};

with:

    Kill the worker in `waiting for handler commit` — not the one in
    `Waiting for dependent transaction to commit`.
    KILL {{PROCESSLIST_ID}};

## GTID gap on the replica

class: lookalike
evidence: "looked exactly like the MTS deadlock … but no `waiting for handler commit` … it was a GTID gap" (2026-10-05)
confidence: low (1 note, 1 snapshot)
add:

    > [!lookalike] GTID gap on the replica
    > Looks like: the MTS deadlock — replica up, IO healthy, lag climbing.
    > Rule out by: no `waiting for handler commit` in `SHOW ENGINE INNODB STATUS`,
    > and `Retrieved_Gtid_Set` ahead of `Executed_Gtid_Set`.

## Run from the host, not the bastion

class: environment fact
evidence: "we always run these from the bastion — the `mysql -e …` lines assume you're already on the host" (2026-10-06)
confidence: low (1 note, 1 snapshot)
add var:

    - id: replica-host
      label: Replica host
      var: REPLICA_HOST
      placeholder: db-2.prod.internal

confirm: is the host stable per deployment, and is "run from the bastion" true
of every operator?

## Out of scope

- "the alert paged the old payments rota" (2026-10-04, 2026-10-06) → alerting/rota config

## No action

- Ran end to end: "worked as written" (2026-10-04)
````

## Revision: apply what you accept

You apply the edits in git. For this page that meant naming the worker to kill, adding the lookalike, and leaving the rota complaint and the bastion note alone — one is not the runbook's job, the other is local to a deployment and the reviewer flagged it as a generalisation, not a shared fact.

````diff [The revision — content/playground/replication-lag.md]
 ### Free the blocked worker
 
-Killing the worker stuck in `waiting for handler commit` releases the lock; the
-coordinator re-dispatches and the workers drain.
+Killing the worker stuck in `waiting for handler commit` releases the lock; the
+coordinator re-dispatches and the workers drain. Kill that worker, not the one in
+`Waiting for dependent transaction to commit`.
 
 ```sql [Release the blocked worker]
 KILL {{PROCESSLIST_ID}};
 ```
+
+> [!lookalike] GTID gap on the replica
+> Looks like: the MTS deadlock — replica up, IO healthy, lag climbing.
+> Rule out by: no `waiting for handler commit` in `SHOW ENGINE INNODB STATUS`, and
+> `Retrieved_Gtid_Set` ahead of `Executed_Gtid_Set`.
````

The lookalike is content, not a step and not a warning. Pasted into the page, it renders as its own block:

> [!lookalike] GTID gap on the replica
> Looks like: the MTS deadlock — replica up, IO healthy, lag climbing.
> Rule out by: no `waiting for handler commit` in `SHOW ENGINE INNODB STATUS`, and `Retrieved_Gtid_Set` ahead of `Executed_Gtid_Set`.

## Now it is less wrong

The next responder rules the GTID gap out with one query, and kills the right worker instead of guessing between two. That is the loop closing: execution is the source of truth, and every run makes the page a little righter.

## Where next

- [Agent access](../operations/agent-access.md)
- [Writing a runbook](writing-runbooks.md)
