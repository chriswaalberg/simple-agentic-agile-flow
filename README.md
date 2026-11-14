# simple-agentic-agile-flow

A lightweight agile flow for Claude Code. Ideas go on a board, Claude grills each one into a spec, builds what you approve, and you sign off on staging.

```
Inbox ─grill─▶ Specified ─you drag─▶ Ready to build ─build─▶ In progress ─push─▶ On staging ─you approve─▶ Done
```

It follows Anthropic's [best practices for Claude Code](https://code.claude.com/docs/en/best-practices): interview to a spec, give Claude a check to run, keep hard rules in a hook, and let a person approve the result. How to split the work (subagents, sessions, worktrees) is left to Claude.

## Skills

| Skill | Start with | What it does |
|---|---|---|
| `setup` | `/simple-agentic-agile-flow:setup` | Creates the board (GitHub Projects or Notion) with its columns and labels, optionally a pre-push hook for your checks, and a short Board section in the project's `CLAUDE.md`. That section is where the flow and its rules live. |
| `grill` | "grill <card>", "grill the inbox", or a new idea | Interviews you in rounds of numbered questions, each with a recommended answer, until the card holds decisions, tasks and one line of proof. On your go it moves to Specified. |
| `build` | "build" | Takes every card in Ready to build, top to bottom: tests, the proof, `/code-review` (and `/simplify`, `/verify` where they fit), push, card to On staging. Stops on a card with an open taste choice; the rest goes on. |

Only you move cards to Ready to build and to Done. `grill` and `build` only act in projects whose `CLAUDE.md` has the Board section, so you can install the plugin once and use it where you want.

## Install

```
/plugin marketplace add chriswaalberg/simple-agentic-agile-flow
/plugin install simple-agentic-agile-flow@simple-agentic-agile-flow
```

## Requirements

- GitHub Projects: the `gh` CLI with the `project` scope (`gh auth refresh -s project`), and a local session: Claude Code cloud sessions can't reach GitHub Projects.
- Notion: the Notion connector.

## Tips

- For a long list, or when you step away, start with a [goal](https://code.claude.com/docs/en/goal) instead of "build", so a separate model checks after every turn whether the work is done and Claude keeps going until it is. `build` suggests the line when it fits: `/goal build every card in Ready to build with the build skill; done when each card is On staging or waits on a question for the user, or after 40 turns`. Use it in auto mode, otherwise Claude still stops to ask permission. `/goal` shows the status, `/goal clear` stops it.

## Credits

`grill` is based on Matt Pocock's `grilling` skill, the interview behind his `grill-me` ([mattpocock/skills](https://github.com/mattpocock/skills)).

## License

MIT. `grill` is adapted from Matt Pocock's [mattpocock/skills](https://github.com/mattpocock/skills), also MIT.
