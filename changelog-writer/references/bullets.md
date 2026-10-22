# Writing bullets

Use this for any release, and when you rewrite or clean up existing bullets.
Examples are real Conductor bullets from 0.84 onward.

## Improvements

| Form | Example |
|---|---|
| Present-tense fact about the UI | "Notifications show the chat's title instead of the branch name." |
| Condition first, then behavior | "When your PR is part of a GitHub stack, the Changes tab lists the stack's PRs so you can view the changes of one or several of them." |
| Imperative, for a new action | "Hover over file names in the Git panel to see their full paths." |
| Gated by a setting | "Turn on Pokes in Settings → Experimental to poke a teammate from their profile page." |
| Added to a list | "Added Claude Haiku 5.5 to the model picker." |
| Faster, and when | "Long chat messages render faster when you reopen a chat or scroll back to them." |
| New behavior vs. old | "Codex waits and retries when OpenAI rate-limits a request instead of failing the turn." |
| A group of shortcuts | "New keyboard shortcuts: ⌘/ opens the model picker, ⌘⇧/ cycles the effort level, ⌘⌥/ opens the shortcuts reference, and ⌘⇧H goes to Home" |

- Start with the thing the user touches (the button, tab, file, command),
  not with "We".
- Say where it applies when that is not everywhere (local or cloud, which
  agent, which plan, which OS).
- A second sentence can give the follow-on effect or a limit: "The order
  syncs between desktop and mobile."

## Fixes

Default form: **"Fixed <the symptom> <when / after / while trigger>."**

- "Fixed a crash loop when opening a chat with a very long history."
- "Fixed the Approve button disappearing from a pending plan after
  restarting Conductor."
- "Fixed run scripts with lots of output, like a Rails server, hanging while
  the Conductor window was in the background."

Quote the exact error text, so users find the entry when they search for
their error:

- "Fixed PR info getting stuck on "Loading PR info…" for repositories on
  GitHub Enterprise."

When the new behavior is clearer than the old bug, write that instead:

- "Cursor chats now start MCP servers defined in a repo's
  `.cursor/mcp.json`."
- "GitHub actions in a sleeping or restarting cloud workspace explain what's
  happening instead of showing "Sandbox version request failed: 503"."

Security and privacy: say what was exposed and to whom, without exploit
steps:

- "Agents in repositories hosted outside github.com no longer receive your
  Conductor GitHub credentials."
- "Fixed security issues: shell command injection through crafted Git branch
  names, clone URLs, and file names in local workflows, and remote browser
  previews accepting untrusted certificates."

Small fixes in one area can share a bullet, up to about three items:
"Fixed Escape dismissal, misplaced or unclickable context menus in dialogs,
menus shifting while opening, and dropdown menus making the window scroll."

## Misc

- Changed default: "New users start with Opus 5.5 as their default Claude
  model."
- Removal, with what replaces it: "Removed the Team page. Teammate profiles
  replace it."
- Moved setting, with the old and new place: "Moved the "Include link to
  workspace in PR description" setting from organization Preferences to
  Settings → Git."
- Bundled versions, in one bullet: "Updated the bundled Claude Code to
  2.1.300, Codex to 0.162.1, Cursor Agent to 1.0.37, and OpenCode to
  2.0.29."

## Credits

- Inline: "Add a button to copy rendered tables as markdown (thanks
  @brian_lovin)"
- A "Prompt Requests" section for user requests that shipped: "Mattt:
  Disable macOS system one-time SMS and email autofills from popping up into
  prompt text box and other inputs."

## Weak and strong bullets

Each row puts a real weak bullet next to a real stronger bullet on a
similar topic.

| Weak | Problem | Strong |
|---|---|---|
| "Fixed `/clear`" | No symptom | "Fixed /clear doing nothing when a workspace had the maximum number of chat tabs open." |
| "Fixed several issues with the MCP panel" | Says nothing about what changed | "Fixed the MCPs popover hanging on "Loading MCP servers…" in OpenCode chats, and OpenCode's own MCP servers missing from the list." |
| "Fixed some instances when the client would freeze" | No trigger | "Fixed Conductor freezing after removing a repository from its settings." |
| "Fixed an issue where typing in the file editor with Find (⌘F) open would scroll ..." | "an issue where" is filler | "Fixed the send button staying disabled after pasting or attaching a file." |
| "Show when chat compaction starts" | Commit-title fragment with no subject | "Stopped background tasks show as "Stop task" in the chat." |
| "Revamped command palette UI" | "Revamped" with no detail | "The command palette is now opaque, wraps keyboard navigation from bottom to top, and keeps the selected item clear of the list edge" |
| "Noisy Roundhouse and workspace connection errors are no longer reported to PostHog." | Internal service and vendor names | Leave it out. If it protects users' data, say what stays private: "Error reports no longer include workspace environment variables or message text when background branch naming fails." |
| "Cloud beta just keeps getting better 🙂" | A teaser with no change in it | Leave it out until there is a change to name. |
