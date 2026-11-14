---
name: setup
description: Sets up simple-agentic-agile-flow in a project, creating the board with its columns and labels and writing the flow's section into CLAUDE.md.
disable-model-invocation: true
---

# Setup

## 1. Ask, in one round

Look up what you can first, then ask only what is left, each with your recommendation:

- Board: an existing one (its link), or a new one on GitHub Projects (recommended; same place as the code) or Notion.
- For a new board: owner and title (default: the repo's owner and name).
- Who approves: the name for "only <name> moves cards to Ready to build and Done".
- Where built work is approved: a staging site (its URL) or pull requests with previews.
- The checks (typecheck, tests) and whether a pre-push hook should run them: recommended, with only the fast ones when CI runs the rest.

## 2. The board

**Existing board**

Read its columns, labels and fields and map each to a role in the section below (inbox, specified, ready to build, in progress, on staging, done; the four labels; source and version). Keep the names the board uses and write them in the section with the role in brackets where they differ, like `Gespecificeerd (Specified)`. Add only what is missing; propose renames, don't do them. Move no cards.

**New on GitHub Projects**

1. `gh auth status` must list the `project` scope. If not, ask the user to run `! gh auth refresh -s project` and wait.
2. `gh project create --owner <owner> --title "<title>"` and `gh project link <number> --owner <owner> --repo <owner>/<repo>`.
3. Set the options of the built-in Status field to the six columns of the section below with the GraphQL mutation `updateProjectV2Field` (`fieldId` from `gh project field-list <number> --owner <owner> --format json`; `singleSelectOptions` with `name`, `color`, `description`). Add a text field Version and the labels Bug, Finding, Grilled, Idea (`gh label create <name> --force`) in one command.
4. Views and workflows can't be set through the API. Give the user the link and ask them to switch the view to Board and turn off the built-in workflows "Item closed" and "Pull request merged", so closing an issue never moves a card to Done.

**New on Notion**

Create a database with the Notion tools: Name; Status (status, the six columns); Type (select: Bug, Finding, Grilled, Idea); Source (text); Version (text).

## 3. The pre-push hook

If the user wants it: a script in `.githooks/pre-push` that runs the checks and exits non-zero on failure, `git config core.hooksPath .githooks`, and the install command in the section below so a fresh clone gets it too.

## 4. Write the section in CLAUDE.md

Add this to the project's `CLAUDE.md` (create it if there is none), filled in. It is the one place the flow is defined; keep it this short.

```markdown
## Board

This project uses simple-agentic-agile-flow (skills `grill` and `build`). The board is the backlog: <link>.

- Columns: Inbox → Specified → Ready to build → In progress → On staging → Done. Only <name> moves cards to Ready to build (the go) and to Done.
- Labels: Bug, Finding, Grilled, Idea. A card is <an issue in <owner>/<repo> on project <n> | a page in <database>>; its title, Status, label, Version and a Source line.
- Read and move cards: <the exact commands or tools, with the ids>. <GitHub: Status is leading, not open/closed; run grill and build in a local session, cloud sessions can't reach GitHub Projects. | Notion: set Status, Type and the name as properties, not in the content, and read them back.>
- Something Claude finds on its own (a bug outside the task, a review finding) becomes a full card with its source: straight to Specified when it holds no choice for <name>, otherwise to the Inbox with the open questions on top. A card <name> says no to is removed.
- Checks: <commands>; <the pre-push hook and how to install it, if any>. Staging: <how a push gets there, the URL, how the version shows>. Committing and pushing to staging never needs asking; production is <name>'s approval.
```

## 5. Finish

Show the board link and the section, and offer to put the first idea in the Inbox. If the project had a backlog elsewhere (`PLAN.md`, `TODO.md`, another board), offer to move its open items as Inbox cards; move nothing without a yes.
