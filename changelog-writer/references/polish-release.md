# Polish and bug-fix release

Use this when the release is mostly fixes and small improvements, with no
single feature that needs a paragraph.

## Title

Give it an honest label. A little humor is fine.

- "A (mostly) boring update" (0.91.0)
- "Lots of little things" (0.74.0)
- "Polish" (0.58.0), "Cloud Polish #1" (0.79.0)
- "Bug Squashathon" (0.76.0)
- "We no longer burn your CPU (hopefully)" (0.7.0), for a release about
  one performance fix

Avoid "Bug fixes" with nothing after it. If one fix stands out, name its
area: "Onboarding Bug Fix" (0.27.2).

## Lead

One or two sentences: which areas the release touched, then the one or two
new things in it.

> We spent this release polishing Conductor and fixing papercuts: dozens of
> fixes for chats, cloud workspaces, PRs, and file sync. Pages also have a
> refreshed layout that floats beside the sidebar, and you can now run shell
> snippets from agent replies with one click. (0.91.0)

If there is a story behind the release, tell it in one line, then lead with
the fix people asked for most:

> Yesterday we had our first annual bug squashathon. Here are the results!
>
> By popular demand, the "Checks" tab is back! (0.76.0)

Skip the lead rather than write "Fixed bugs." (0.59.0).

## Order of Fixes

1. Crashes, freezes, and data loss.
2. Security and privacy.
3. Then group by area (chats, sync, PRs, git and diffs, files, sidebar and
   navigation, visual details), so related bullets sit together.

## Example (0.91.0, trimmed)

```markdown
## A (mostly) boring update

We spent this release polishing Conductor and fixing papercuts: dozens of
fixes for chats, cloud workspaces, PRs, and file sync. Pages also have a
refreshed layout that floats beside the sidebar, and you can now run shell
snippets from agent replies with one click.

### Improvements

- Bash snippets in agent replies and Markdown previews have a Run button
  that runs them and shows the output inline.
- Added Claude Haiku 5.5 to the model picker.
- When your PR is part of a GitHub stack, the Changes tab lists the stack's
  PRs so you can view the changes of one or several of them.
- Drag workspaces to reorder them in the Pinned section. The order syncs
  between desktop and mobile.
- Codex waits and retries when OpenAI rate-limits a request instead of
  failing the turn.
- Long chat messages render faster when you reopen a chat or scroll back to
  them.
- Turn on Pokes in Settings → Experimental to poke a teammate from their
  profile page, and poke them back when they poke you. Pokes only work when
  both of you have the setting on.

### Fixes

- Fixed a crash loop when opening a chat with a very long history.
- Fixed a new cloud workspace's first message sometimes getting lost if
  Conductor quit while the workspace was being created.
- Agents in repositories hosted outside github.com no longer receive your
  Conductor GitHub credentials.
- Error reports no longer include workspace environment variables or message
  text when background branch naming fails.
- Fixed merging a PR from a sleeping cloud workspace failing with "Code in
  this workspace may be different from what's on GitHub".
- Fixed cloud file sync getting stuck re-downloading the same files with an
  authentication error when syncing many files at once.
- Fixed /clear doing nothing when a workspace had the maximum number of chat
  tabs open.
- Fixed the send button staying disabled after pasting or attaching a file.

### Misc

- New users start with Opus 5.5 as their default Claude model.
- Removed the Team page. Teammate profiles replace it.
- Updated the Conductor app icon and the Linear MCP server icon.
- Updated the bundled Claude Code to 2.1.300, Codex to 0.162.1, Cursor Agent
  to 1.0.37, and OpenCode to 2.0.29.
```

Why it works:
- The title sets expectations, and the lead still names the two new things.
- Fixes go from crash, to lost data, to security, then by area.
- Every fix names a symptom, and most name a trigger.
- Misc holds defaults, removals, icons, and versions, so Improvements stays
  about new abilities.

## Counter-example (0.79.0, Fixes excerpt)

```markdown
- Fixed several issues causing message replies to be lost
- Fixed several issues related to session state
- Fixed several issues with file sync and improved error messages
```

Three bullets, and the reader still can't tell whether their bug is fixed.
Name each symptom, as in "Fixed cloud file sync getting stuck re-downloading
the same files ..." above, or leave the bullet out.
