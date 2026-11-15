---
name: build
description: Builds every card in "Ready to build", top to bottom, until each is on staging. Use when the user says "build" or asks to build a card from the board, in a project whose CLAUDE.md has a simple-agentic-agile-flow Board section.
---

# Build

Read the Ready to build column once, with all card bodies, and take the cards top to bottom. Build cards that touch the same files one after the other.

With more than three cards and no goal active: before the first card, ask one question with buttons, using the host's question tool (never as text between tool calls, where it scrolls out of sight): "Keep building until every card is On staging or waits on a question?", with the buttons "Keep building" and "Card by card". When the user says they are stepping away, don't ask; take it as "Keep building".

On "Keep building": a card that waits on the user does not end your turn. Park its question, go on with the next card, and ask all parked questions together at the end, each with buttons. Stop after 40 turns.

For each card:

1. Move it to In progress. Tell the user in a few plain sentences what you are building and why.
2. If it has state or timing, write down three to five edge cases first (repeat, restart, empty list, at the same time) and cover them in the tests.
3. Build it with tests and show the proof the card asks for: the command and its output, or for something visible, `/verify` with its screenshot.
4. Review with `/code-review` (one review may cover several small cards); for a large diff also `/simplify`.
5. Commit, run the project's checks and push, without asking: the user's go on the card covers it, and the user approves on staging. In one update: card to On staging with the version, its tasks ticked, and a note of what is still open.

Stop on a card and ask the user when a taste choice is still open, when it brings something new to look at (a design, an image, an animation) that the user hasn't seen, after two failed attempts, or before anything irreversible or paid. The other cards go on.

A bug in what you just built: fix it right away, no card.

At the end, one line per card: the version, the proof, and what is still open.
