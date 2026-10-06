---
name: adr
description: Create or update an Architecture Decision Record. Use when a significant architectural or process decision is being made, when an earlier decision needs to be reversed or refined, or when the user asks to "write an ADR", "record this decision", "log this decision", «заведи ADR», «запиши решение». Finds the ADR directory on its own, follows the project's conventions from CLAUDE.md / AGENTS.md / GEMINI.md, and sets Supersedes/Amended by links in the affected records.
---

# ADR — create or update

An ADR exists for one thing only: **never argue the same thing twice**. Everything
else in this skill serves that goal. A record with no rejected alternatives and
no reasons for rejecting them is not doing its job.

## Step 1. Find the directory and the conventions

```bash
ls adr/ 2>/dev/null || ls docs/adr/ 2>/dev/null || ls doc/adr/ 2>/dev/null
```

No directory — ask where to create it; don't invent a location.

Check the project's agent instructions file — `CLAUDE.md`, `AGENTS.md` or
`GEMINI.md`, whichever exists — for a section about ADRs. **Project conventions beat this
skill's defaults**: if the project has set its own title format, language or
numbering scheme — follow the project. `Firmness`, `Revisit if` and `Record` are
additive: keep them even when the project's template doesn't list them, unless
it explicitly says not to.

## Step 2. Check prior decisions, but don't read everything

Read the **titles** of existing ADRs — the file names already carry the topic.
Open only those whose names are directly relevant to the task. Walking through
every ADR is burned context and the typical mistake at this step.

What you're looking for: has this question been decided before; which decision
does the new one reverse or refine; which neighbouring records should it link to.

## Step 3. Number

The next free sequence number, with the same digit count as its neighbours
(`0013-`, not `13-`). File name — a short kebab-case of the decision's essence,
in the language of the other records in the directory.

```bash
ls adr/ | sed -E 's/^([0-9]+).*/\1/' | sort -n | tail -1
```

## Step 4. Format — strictly

```markdown
# [Title: the decision itself, not the topic]

Date: YYYY-MM-DD
Firmness: tentative         ← tentative | settled
Revisit if: [condition]     ← mandatory for tentative
Supersedes: NNNN            ← only if it reverses one
Status: Amended by NNNN     ← added later, when this record gets refined

## Context

Why the question came up at all. Facts, numbers, investigation. What broke or
what was missing. Rejected alternatives go here too, and **why** they were
rejected: in six months this is all that will be left.

## Decision

What was decided. Affirmative, present tense. Numbered points if the decision
is compound.

## Consequences

Pros, cons, technical consequences. Cons are mandatory — an ADR without cons
is not a decision, it's an advertisement. A separate "Follow-up:" line — the
questions this decision opens that will have to be closed separately.

## Record

> "verbatim quote from the conversation" — user
> "a doubt nobody answered" — user (unresolved)
```

The title is a statement: not "The live table transport question", but
"The live table is delivered over SSE".

### Firmness

The prose of an ADR always reads more confident than the conversation was. Firmness is
how a future reader tells a commitment from an experiment.

- `settled` — the user committed, and no raised doubt was left open.
- `tentative` — "let's go with X for now", "let's try", an open objection, a choice
  made under time pressure. **When unsure, it's tentative.**

`Revisit if:` is a concrete, checkable trigger ("a second API client appears",
"p95 latency over 200 ms"), not "if needed". A tentative decision without one
is just a shrug.

### Record

The Context is your retelling; the Record is the evidence. Two to five
**verbatim** quotes from the conversation, mostly the user's words.

- Every doubt or objection that was raised and not refuted goes in, marked
  `(unresolved)`. Quoting only the line that sealed the decision defeats the purpose.
- Verbatim, in the original language. If you can't quote it, don't paraphrase
  it into quotation marks — put it in Context instead.
- No secrets, tokens, or third parties' personal details.
- Written after the fact with no conversation to quote — say so:
  `Record: none, reconstructed from code`. That, too, tells the reader something.

## Step 5. Repair the history

When a new decision touches an old one — **edit the old file**, don't leave it lying:

- reverses it entirely → old gets `Status: Superseded by NNNN`, new gets `Supersedes: NNNN`;
- refines or narrows it → old gets `Status: Amended by NNNN (in parentheses — what exactly)`.

The line goes right after `Date:`. The old text is **not rewritten**: it remains
evidence that back then people thought otherwise. That's the whole value.

## Step 6. Say it in chat

One line: number, title, what was touched. Don't retell the content — it's
in the file.

## Precedent, not scripture

When an existing ADR comes up — the user questions it, or it blocks what's being
asked — don't defend it on reflex. Read its `Firmness`, `Revisit if` and `Record` first.

- `tentative`, or its `Revisit if` has fired — the question is open. Say so.
- The facts in its Context no longer hold — point that out; the decision stood on them.
- No `Firmness` line — the record predates the field. Its confidence is unknown,
  not settled.
- `settled` and still true — cite it, and say what new fact would justify
  reopening it. That's stare decisis; refusing to discuss it is dogma.

## Pitfalls

- **A recap instead of a decision.** "We discussed options, leaning towards…" is not an ADR.
  If no decision has been made, there's nothing to write; come back when it is.
- **Empty Context.** Six months later, "we decided X" without "because Y" reads
  as arbitrary, and the argument starts all over — exactly what this skill exists to prevent.
- **Retroactive edits.** Changed your mind — a new ADR with a link, not an edit
  of the old text. The only thing appended to an old file is the Status line.
- **Smoothing over doubt.** "Let's try SSE for now" written up as "SSE was chosen
  because…" with `Firmness: settled`. Next session treats a guess as law.
- **A cherry-picked Record.** Only the quotes that support the decision. The
  objections are the part a future reader needs most.
- **Reading every ADR.** Expensive and almost always unnecessary. Titles, then two
  or three relevant files.
- **ADRs about taste.** A button colour is not a decision. A decision is something
  people will be tempted to come back to and re-argue.
- **Context pushed out into the issue tracker.** Long rationales, slice breakdowns
  and rejected options live in the ADR. The ticket carries a link to the number, not a retelling.
