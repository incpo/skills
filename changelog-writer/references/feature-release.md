# Feature release

Use this when the release has at least one new feature worth a paragraph:
a launch, a partnership, a new ability, or a changed default people will
notice. Version numbers point to real Conductor entries
(conductor.build/changelog/<version>-<title-slug>).

## Title

Name the feature, or what the user can now do. Sentence case, no version,
at most three items.

- One feature: "Stacks" (0.80.0), "Conductor MCP" (0.82.0)
- What the user can now do: "Edit files directly in diffs" (0.84.0), "Share
  your loadouts" (0.86.0)
- Two or three features: "Search across files, OpenCode 2.0" (0.87.0),
  "Sections, routines, and a new model picker" (0.85.0)
- A launch: "Introducing Conductor Cloud" (0.78.0), "Conductor for iOS"
  (0.90.0)

## Lead

The lead goes right after the hero image. It says what is new and how to
start using it. The shortest form is one paragraph:

> You can now search across files! Press ⌘⇧F in any local or cloud
> workspace to search file contents, with case, whole-word, and regex
> toggles. (0.87.0)

If access is limited, say so: "Routines run in the cloud (which means
they're only available to pro/team users)." (0.85.0)

If a default changed, say how to go back:

> Changed files now open and edit directly in diffs by default. To return to
> the previous file preview and editor, turn on "Use legacy file preview and
> editing" in Settings → Experimental. (0.84.0)

## Example 1: one headline + "Other big improvements" (0.89.0)

```markdown
## Sign in with ChatGPT

[image: Sign in with ChatGPT]

We're excited to be partnering with OpenAI on Sign in with ChatGPT.

You can now bring your ChatGPT plan to Conductor in a few clicks. Go to
Settings → Agents → Codex and click Continue with ChatGPT to start using your
Codex plan in Conductor.

Other big improvements:

- The PR view has a new layout. One card at the top shows the branch, review,
  checks, and merge status, with the description and activity below it.
- New workspaces get descriptive branch names based on what you're working
  on. To turn this off, go to Settings → Git → Automatically rename new
  branches.
- You can now steer Cursor chats while they run.

### Improvements

- In the PR view, open the Review row to add unresolved comments to chat, or
  the Checks row to re-run or fix failed checks.
- Merging a PR from Conductor now only merges the code that's in your
  workspace. If the PR on GitHub has changed, the merge is blocked and you
  get a link to merge on GitHub instead.
- Deep links can include a `branch` parameter to start a new workspace from
  an existing branch.

### Fixes

- Fixed high memory use that grew as you opened more chats.
- Fixed run scripts with lots of output, like a Rails server, hanging while
  the Conductor window was in the background.

### Misc

- Moved the "Include link to workspace in PR description" setting from
  organization Preferences to Settings → Git.
- Updated the bundled Codex to 0.157.1, OpenCode to 2.0.18, and Cursor Agent
  to 1.0.32.
```

Why it works:
- The lead gives the exact click path.
- "Other big improvements:" puts three changes above the long list. They
  can appear again, in more detail, under Improvements.
- The new default comes with how to turn it off.

## Example 2: several headline features, each in its own section (0.85.0)

```markdown
## Sections, routines, and a new model picker

### Sections

We've added a new way to organize your sidebar: sections! Right click a
section header to edit or create a new one.

[image: Sidebar sections with options to edit, create, or delete a section]

### A new model picker

Introducing our new model picker! Pick a loadout of your favorite models to
quickly switch between. It's keyboard accessible too: change models (⌃⌘ 1-5),
effort (⌘⇧/), speed (⌘⇧E) or model ordering (Tab, then space) without leaving
your keyboard.

[image: Model picker showing favorite models, effort levels, and the Fast toggle]

### Routines

Introducing routines! You can now run your agents on a schedule or via
GitHub action. We use routines to write internal reports, help with daily
stand-up, and find papercuts in our system prompts. Routines run in the
cloud (which means they're only available to pro/team users).

[image: A scheduled routine with its prompt and completed runs]

### Private workspaces

You can now create private workspaces that aren't shared with the rest of
your team.

### Improvements
...
```

Why it works:
- Each section has two or three sentences: what the feature is, how to use
  it, and a limit or a real use.
- "We use routines to ..." shows the feature through the team's own use.
- The title lists three features. The fourth gets a section only.

## Example 3: a launch (0.78.0)

```markdown
## Introducing Conductor Cloud

[image: Clouds arranged in the shape of the Conductor logo against a blue sky]

Conductor Cloud is here!

Cloud workspaces run in isolated sandboxes, so agents keep working when you
close the app or step away from your computer.

To get started, sign up for Pro and create an organization.

### Work from anywhere, together

[image: A Conductor Cloud workspace where one teammate asks another to review documentation changes]

Workspaces keep going when you close your laptop and can be opened from the
desktop app, the mobile app (coming soon), or the Conductor API. Share a
workspace link (⌘⇧C) with teammates, see who is active, follow work you care
about, and prompt agents together in real time.

### API

Today we're also launching the Conductor API, which lets you manage cloud
workspaces programmatically. Use it to do things like:

- Create workspaces, send prompts to the coding agent, and read its replies.
- Build bots that kick off agent work.
- Run and manage Conductor workspaces from your OpenClaw.
```

Why it works:
- Three short lines: the announcement, the main benefit, the first step.
- "Use it to do things like:" lists real tasks, not features.

## Example 4: a feature agents or developers use, shown with prompts (0.82.0)

```markdown
## Conductor MCP

[image: Conductor MCP capabilities in an agent session]

Introducing the Conductor MCP! Agents can use the Conductor MCP to create and
manage cloud workspaces, read sessions, and more. Add the MCP anywhere you
run your agents.

Try asking your agent to:

- "Review my workspace diff and leave inline comments."
- "Address the comments in the Changes panel."
- "Check why the dev server failed."
```

Why it works: for a tool or an API, a few example requests explain it
faster than a description. For a CLI or a library, use a short command or
code sample in the same place.

## Heads-up and breaking changes

Put anything users must act on near the top, with what to do instead.

- In an app: one "Heads-up:" line after the lead, or the change in the lead
  itself (0.84.0 above).
- A removal that changes how people work, with the replacement: "Removed
  Codex goals and message queuing. Messages sent while an agent is working
  now steer it instead of waiting in a queue." (0.86.0)
- For CLIs, libraries, and APIs, start with a `### Breaking changes`
  section. Give the migration for each item ("Removed `x`. Use `y`
  instead.").
