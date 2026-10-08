---
title: Component gallery (retroencabulator drift)
slug: component-gallery
order: 2
description: Every frontmatter field and every body block the renderer supports, shown on a procedure that is entirely fabricated.
symptoms:
  - component gallery
  - every block type
  - design system
  - retroencabulator
notice: "Design fixture: none of this is a real procedure. The Retroencabulator does not exist, and neither do its spurving bearings."
vars:
  - id: gallery-host
    label: Retroencabulator host
    var: HOST
    placeholder: "e.g. encabulator-01.lab.internal"
    hint: |-
      Where the assembly lives.
      In this fixture any value works.
  - id: gallery-torque
    label: Spurving bearing torque (Nm)
    var: SPURVING_TORQUE
    placeholder: "e.g. 42"
    hint: The load the differential girdle spring will accept.
  - id: gallery-token
    label: Transcription API token
    var: API_TOKEN
    secret: true
    hint: Rendered as a password field. Values never leave the page.
---

This page is the component gallery. Every heading, block and frontmatter field the renderer supports appears below, dressed as a procedure for the Retroencabulator: a machine with a base plate of pre-famulated amulite, a malleable logarithmic casing, and no reason to exist. **Nothing here is real.** Read it to see how each component renders, not to fix anything.

The prose before the first heading renders like this, and it can carry **bold**, *italic*, `inline code`, and a [link](https://en.wikipedia.org/wiki/Turbo_encabulator).

## Survey the casing and bearings

A `##` heading starts a step: a numbered, tickable, collapsible card. Its body can hold prose, lists and tables.

- The logarithmic casing is **malleable**, which is the entire point of it.
- The `panametric fan` sits *directly* above the two spurving bearings.
- The differential girdle spring is tensioned to the torque you entered (the `SPURVING_TORQUE` input) and left there.
- A bullet may nest another list:
  - The lunar wane shaft is aligned with the main axis.
  - The reciprocating dingle arm is not to be oiled.

A numbered list works the same way:

1. Confirm the assembly is unplugged.
2. Confirm it was never plugged in.
3. Proceed regardless.

| Reading | Meaning | Expected |
| --- | --- | --- |
| `amulite drift` | Base plate creep | `< 0.02 mm` |
| `gyro wobble` | Fan precession | `±3 °` |
| `girdle tension` | Spring load | e.g. `42 Nm` |

### A subheading keeps the step going

A `###` subheading splits a long step without starting a new card, so related readings stay under one number.

## Re-tension the girdle spring

Code blocks take a lower-case language tag and an optional bracketed label. The label is what renders the header with the per-block done checkbox; Runbooks highlights the variables you have filled in.

Variables substitute **in code blocks only** (and this page keeps its tokens there), so Runbooks leaves a `{{TOKEN}}` in ordinary prose or a table cell exactly as you typed it.

```bash [Torque the spring]
RETRO_HOST="{{HOST}}"
retroctl tension --host "$RETRO_HOST" --torque {{SPURVING_TORQUE}} --spring girdle
```

```sql [Audit the bearings]
SELECT bearing, torque_nm, drift
FROM   retro.spurving
WHERE  drift > 0.02
ORDER  BY drift DESC;
```

```text
Any lower-case language tag is accepted. With no bracketed label (this fence has none) there is no header and no checkoff, which suits output rather than a command.
```

```bash [Uses the secret]
retroctl transcribe --token "{{API_TOKEN}}" --endpoint "https://{{HOST}}/encabulate"
```

## Decide whether to continue

The three notice variants, then a decision callout.

> [!info] An info notice carries context, links and read-only remarks.

> [!warn] A warning is for a step that briefly stops something, or a value worth double-checking.

> [!danger] A danger notice is for the destructive step. READ EVERY STEP BEFORE DOING ANYTHING.

> [!branch]
> - If the gyro wobble is within tolerance, continue to the next step.
> - If the dingle arm has fractured, replace it and begin again.
> - If neither is true, do not touch the Retroencabulator again.

---rollback

## De-encabulate the assembly

Everything after `---rollback` renders in the danger styling and without a number, because it runs only when the procedure above has gone wrong.

```bash [Reverse the tension]
retroctl tension --host "{{HOST}}" --torque 0 --spring girdle
```

## Confirm the wobble has stopped

A second rollback step, so the gap between two consecutive danger cards is visible.

<!-- sections -->

## Appendix: the rest of the content model

A `<!-- sections -->` line (or `---sections`) switches the headings after it to unnumbered **sections**, the same content model used for reference prose. Switch back with `<!-- steps -->`. This page is a procedure, so only the appendix is a section.

The acknowledgement gate is the one component not shown here: setting `acknowledge:` in frontmatter opens the page behind an "I understand" dialog, which a gallery should not do to its reader. See [Writing a runbook](writing-runbooks.md) for every field.
