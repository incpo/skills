# Small release, hotfix, or patch

Use this for a release with one change: a new model or integration, one
small feature, a hotfix, or a patch that follows a bigger release.

## New model or integration

Two lines: say it is available, then compare it to something the reader
knows (quality, speed, price). Say if it is now the default.

```markdown
## Claude Opus 5.5

[image: Claude Opus 5.5]

Claude Opus 5.5 is now available in Conductor!

Opus 5.5 performs at the level of Claude Fable 5.1 for most tasks and costs
40% less to run than Opus 5.
```

```markdown
## GPT-6.1 Sol

[image: GPT-6.1 Sol]

GPT-6.1 Sol is now available in Conductor!

OpenAI's upgrade to GPT-6 Sol nearly matches GPT-6 Astra on agentic coding at
one-fifth of Astra's price. It's also the new default Codex model.
```

If access depends on a provider or plan, say how to get it: "Grok 4.7 is now
available in Conductor via Cursor. Give it a try!" (0.87.1)

## One small feature

One sentence on the feature. Use "Also in this release:" for anything else.
Leave out section headings.

```markdown
## Model search

You can now search all available models from the model picker in chats, new
workspaces, and Routines.

Also in this release: the bundled Codex is updated to 0.156.1.
```

## Hotfix

Name the bug and who it affected. Apologize when people lost time.

```markdown
## Bug Fix

We fixed a bug that caused Claude to use your ANTHROPIC_API_KEY if one was
set in your environment, even if you were on a Pro/Max/Team account.
```

```markdown
## Onboarding Bug Fix

We've fixed a bug that would cause Claude Code authentication to hang for
some users during onboarding. This bug also resulted in slash commands not
populating in new chats. Our apologies!
```

If users can help with an upstream bug, point them to it: "Please upvote
this issue for Anthropic to make a proper fix!" (0.29.5)

## Patch after a bigger release

Add patches to the bottom of the entry they follow, under
`#### New in x.y.z`, instead of making a new entry. The bullet rules stay
the same.

```markdown
#### New in 0.89.2

- Fixed PR info getting stuck on "Loading PR info…" for repositories on
  GitHub Enterprise Server 3.21 and earlier.

#### New in 0.89.3

- Fixed chats getting stuck in progress, Stop doing nothing, and model
  settings being locked on macOS versions before 14.4.
```

A patch can carry a small teaser:

```markdown
#### New in 0.91.2

- Psst! You can now control your Mac from the Conductor iOS app. Turn on
  Remote control in Settings → Experimental, pair your phone, and keep
  chatting with agents in your local workspaces from anywhere. Learn how to
  set it up.
```

A patch that ships a model or one notable feature gets its own short entry
(see above). Later patches go under that entry.
