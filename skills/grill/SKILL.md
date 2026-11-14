---
name: grill
description: Turns an idea or an Inbox card into a full spec on the board by interviewing the user in rounds. Use for "grill <card>", "grill the inbox", or a new idea for the board, in a project whose CLAUDE.md has a simple-agentic-agile-flow Board section.
---

# Grill

A new idea first becomes a card in the Inbox (a title and one line), so it is never lost. "Grill the inbox" takes those cards one at a time, top to bottom.

For an existing card, first look up what was already decided (the card, the documents it points to, the git log) and grill only the open points. Open with three to five sentences: what the card is for, what is decided and built, what is still open.

## The interview

Interview the user until you reach a shared understanding. Map it as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are settled. Ask the whole frontier in one round, numbered, each with your recommended answer, worded so that "yes" accepts it. After the answers, recompute the frontier and ask the next round.

The root of every tree: **will a user realistically notice this?** Answer it yourself when it is obvious. When it is in doubt (usually a refinement of something that exists), ask it first and show what exists now next to what would change. If no, the idea stops or shrinks to what a user would notice.

```
❓ **Q1** - **<question title>**: <question, with the options if there are several>

➡️ <your recommended answer>

---

❓ **Q2** - ...
```

Facts are your job: look them up instead of asking. A fact still being looked up only holds back the questions that depend on it. Decisions are the user's. You are done when the frontier is empty and nothing is left silently assumed.

## The card

Write the result as the card, in plain language that someone who doesn't know the code can follow:

```
<Two or three sentences: what and why.>

## Decisions
1. <one line each, numbered straight through, no history or reasoning>

## Small decisions I made
- <one line each; the user can object>

## Tasks
- [ ] <one checkbox per piece of work>

## Proof
<One line: how we will see it works. A command and what it should print, or a screenshot of what.>

## Source
<Where the idea came from.>

## Notes for the builder
<Optional: technical pointers, files, pitfalls.>
```

Show it and ask for go. On go: put it on the card, label it Grilled and move it to Specified. Don't build.
