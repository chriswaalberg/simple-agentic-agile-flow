---
name: setup
description: Sets up simple-agentic-agile-flow in a project, creating the board with its columns and labels, a staging environment when there is none, and writing the flow's section into CLAUDE.md.
disable-model-invocation: true
---

# Setup

## 1. Ask, in one round

Look up what you can first, then ask only what is left, each with your recommendation:

- Board: an existing one (its link), or a new one on GitHub Projects (recommended; same place as the code) or Notion.
- For a new board: owner and title (default: the repo's owner and name).
- Who approves: the name for "only <name> moves cards to Ready to build and Done".
- Staging: the URL where built work is approved. If the project has none, propose a staging repo (step 3).
- How a card gets to staging: pushed straight to the main branch (recommended; you approve on staging), or optionally a branch and a pull request per card that <name> merges, for a check before staging.
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

## 3. Staging

Skip this when the project already has a place to approve built work.

Otherwise propose a lightweight staging repo on GitHub Pages: a second, private repo `<owner>/<repo>-staging` that only serves the staging build. Every push to the main branch builds once and puts that build on staging; after the user's Approve in GitHub exactly that build goes live. Ask for a go first: it creates a repo and a key and changes repo settings. Then:

1. `gh repo create <owner>/<repo>-staging --private --description "Staging of <repo>: filled by the Pages workflow of <owner>/<repo>. Don't edit by hand."`
2. A key pair just for this (`ssh-keygen -t ed25519 -N "" -f <scratch>/staging_key`): the public key as a deploy key with write access on the staging repo (`gh repo deploy-key add --allow-write`), the private key as the secret `STAGING_DEPLOY_KEY` in the main repo (`gh secret set`). Delete both files afterwards.
3. Add `staging-workflow.yml` (next to this skill) as `.github/workflows/pages.yml`, with the project's checks and build filled in. A site without a build copies its public files into a folder; keep paths relative, so the same build works on staging and live.
4. Push it. The first run puts a `gh-pages` branch in the staging repo, and GitHub turns on Pages from it. A private repo gets private Pages: a generated address (`gh api repos/<owner>/<repo>-staging/pages --jq .html_url`) that only people with access to the repo can open, after a GitHub login. That address is the staging URL; put it in the workflow.
5. Create the environment `github-pages` in the main repo with the user as required reviewer (`gh api -X PUT repos/<owner>/<repo>/environments/github-pages` with `reviewers`).
6. Last, only when staging is green: switch the main repo's Pages source to GitHub Actions (`gh api -X PUT repos/<owner>/<repo>/pages -f build_type=workflow`); a custom domain stays. Until then the live site keeps running as it did. Run the workflow once (`gh workflow run pages.yml`), let the user approve, and check the live site.

## 4. The pre-push hook

If the user wants it: a script in `.githooks/pre-push` that runs the checks and exits non-zero on failure, `git config core.hooksPath .githooks`, and the install command in the section below so a fresh clone gets it too.

## 5. Write the section in CLAUDE.md

Add this to the project's `CLAUDE.md` (create it if there is none), filled in. It is the one place the flow is defined; keep it this short.

```markdown
## Board

This project uses simple-agentic-agile-flow (skills `grill` and `build`). The board is the backlog: <link>.

- Columns: Inbox → Specified → Ready to build → In progress → On staging → Done. Only <name> moves cards to Ready to build (the go) and to Done.
- Labels: Bug, Finding, Grilled, Idea. A card is <an issue in <owner>/<repo> on project <n> | a page in <database>>; its title, Status, label, Version and a Source line.
- Read and move cards: <the exact commands or tools, with the ids>. <GitHub: Status is leading, not open/closed; run grill and build in a local session, cloud sessions can't reach GitHub Projects. | Notion: set Status, Type and the name as properties, not in the content, and read them back.>
- Open issues in <owner>/<repo> that are not on the board are a second inbox: grill takes them after the Inbox. On go the issue <itself goes on the board as Specified | becomes a card with the issue as Source>. At each step (Specified, On staging with the version, Done) post one short comment on the issue in plain language, so whoever filed it can follow; at Done close the issue.
- Something Claude finds on its own (a bug outside the task, a review finding) becomes a full card with its source: straight to Specified when it holds no choice for <name>, otherwise to the Inbox with the open questions on top. A card <name> says no to is removed.
- Checks: <commands>; <the pre-push hook and how to install it, if any>. Staging: <how a push gets there, the URL, how the version shows>. <Straight: committing and pushing to staging never needs asking. | PR: each card is a branch and a pull request; <name> merges it, which puts it on staging.> Production is <name>'s approval.
```

## 6. Finish

Show the board link, the staging URL and the section, and offer to put the first idea in the Inbox. Open issues stay where they are: they are the second inbox. If the project had a backlog elsewhere (`PLAN.md`, `TODO.md`, another board), offer to move its open items as Inbox cards; move nothing without a yes.
