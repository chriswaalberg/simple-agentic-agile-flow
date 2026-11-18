# simple-agentic-agile-flow

A lightweight agile flow for Claude Code. Ideas go on a board, Claude grills each one into a spec and builds what you approve, and you sign off on staging.

```
Inbox ─grill─▶ Specified ─you drag─▶ Ready to build ─build─▶ In progress ─push─▶ On staging ─you approve─▶ Done
```

Only you move cards to Ready to build and to Done. The flow follows Anthropic's [best practices for Claude Code](https://code.claude.com/docs/en/best-practices).

## Get started

1. Add the marketplace:

   ```
   /plugin marketplace add chriswaalberg/simple-agentic-agile-flow
   ```

2. Install the plugin, for all your projects:

   ```
   /plugin install simple-agentic-agile-flow@simple-agentic-agile-flow
   ```

   Or for one project: add `--scope project` and commit the `.claude/settings.json` it creates. Then every checkout and worktree of that project has the plugin.

3. In each project where you want the flow, start a new session and run `/simple-agentic-agile-flow:setup`. It creates the board and a Board section in `CLAUDE.md`. `grill` and `build` only work in projects with that section.

## Skills

| Skill | Start with | What it does |
|---|---|---|
| `setup` | `/simple-agentic-agile-flow:setup` | Creates the board (GitHub Projects or Notion), the Board section in `CLAUDE.md`, and optionally a pre-push hook for your checks. No staging yet? It proposes a private staging repo on GitHub Pages, with approval before going live. |
| `grill` | "grill <card>", "grill the inbox", or a new idea | Asks you questions in rounds, each with a recommended answer, until the card is a spec. On your go it moves to Specified. Open GitHub issues are a second inbox; whoever filed one sees the progress in the issue. |
| `build` | "build" | Builds every card in Ready to build, top to bottom: tests, review, push, card to On staging. Skips a card that waits on a choice from you. |

## Requirements

- GitHub Projects: the `gh` CLI with the `project` scope (`gh auth refresh -s project`), in a local session (cloud sessions can't reach GitHub Projects).
- Notion: the Notion connector.

## Tip

For a long list, or when you step away, start with a [goal](https://code.claude.com/docs/en/goal) instead of "build", in auto mode:

```
/goal build every card in Ready to build with the build skill; done when each card is On staging or waits on a question for the user, or after 40 turns
```

## Credits and license

MIT (`LICENSE`). `grill` is adapted from Matt Pocock's `grilling` skill in [mattpocock/skills](https://github.com/mattpocock/skills), also MIT; his notice is in `NOTICE`.
