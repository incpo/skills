---
name: changelog-writer
description: Write user-facing changelogs and release notes in the style of Conductor's changelog (conductor.build/changelog), with curated real examples for feature launches, polish releases, model drops, hotfixes, and patches. Turns git logs, merged PRs, commit lists, or rough notes into a titled release entry with a lead, Improvements, Fixes, and Misc. Use whenever the user asks for a changelog, release notes, "what's new", a version announcement, a patch note, or to rewrite or clean up existing changelog bullets, even if they only paste a list of commits or PR titles and say "write this up".
---

# Changelog writer

Write release notes that tell a user what they can now do, what stopped
breaking, and where to find it. The model is Conductor's changelog: a short
title, a lead paragraph about the headline change, then plain one-line
bullets under Improvements, Fixes, and Misc. The references hold the best
real entries, grouped by type of release.

## Workflow

1. **Collect the raw changes.** Use what the user gives you. If they point at
   a repo, read the changes since the last release: `git tag --sort=-v:refname`
   to find it, then `git log <last-tag>..HEAD --no-merges --format='%h %s%n%b'`
   or `gh pr list --state merged --search "merged:>=<date>"`. If a commit
   title does not say what the user sees, read the diff or the PR body. Never
   invent behavior to fill a gap. Ask, or leave the item out.
2. **Filter.** Keep only changes a user can see or feel. Drop refactors,
   tests, CI, internal tooling, and dependency bumps, unless the dependency is
   a bundled tool users know (in Conductor: "Updated the bundled Claude Code
   to 2.1.300"). Drop fixes for bugs that never shipped. Merge follow-up
   commits into one item.
3. **Pick the case** and read its reference (see References). Read
   `references/bullets.md` too.
4. **Sort into buckets.**
   - **Headline**: the one to three changes worth a picture and a paragraph.
   - **Heads-up**: changes that make users act: removed features or
     commands, changed defaults most users will notice, and anything that
     breaks scripts, config, or an API.
   - **Improvements**: new abilities and visible changes to how things work.
   - **Fixes**: things that were broken for users and now work.
   - **Misc**: changed defaults, removals, moved settings, renames, icon and
     copy tweaks, bundled versions.
5. **Pick the title** and **write the lead** using the case reference.
6. **Write the bullets** by the rules below.
7. **Order** each section by user impact, then group by area. Put crashes,
   data loss, and security first in Fixes.
8. **Check** the draft against the checklist at the end.

If the project already has a changelog, match its file, headings, date
format, and tone, and apply the bullet rules inside its structure. Use the
Conductor layout only when there is no existing format.

## Entry layout

```markdown
## <Title>

`<version>` · <date>

[image: <what the screenshot shows>]

<Lead: 1–3 short paragraphs about the headline change.>

Heads-up: <what was removed or changed, and what to do instead.>

Other big improvements:

- <3–5 highlights, only for big releases>

### Improvements

- ...

### Fixes

- ...

### Misc

- ...

#### New in <patch version>

- <later patch changes, appended to the minor release they patch>
```

- Leave out empty sections. A one-change release can be one sentence:
  "We fixed a bug causing slow startup times."
- With two or more headline features, give each its own `###` section with
  a sentence or two and an image, before Improvements.
- Patch releases go under `#### New in x.y.z` at the bottom of the entry they
  patch, so readers see one entry per real release.
- Add an image placeholder only when the product has a UI and the headline
  can be shown. Describe what the image shows ("Search in files results for
  useQuery, grouped by file"). The user will add the screenshot. For CLIs,
  libraries, and APIs, show a short command or code sample instead, or
  nothing.
- Put Heads-up items right after the lead, one sentence each, with the
  replacement or the way back. For CLIs, libraries, and APIs, use a
  `### Breaking changes` section first instead. Small removals and default
  changes that need no action stay in Misc.

## Bullet rules

1. **One user-visible change per bullet.** One or two sentences. End with a
   period. Combine items only when they are in the same area and short.
2. **Say what the user sees, in present tense.** "Notifications show the
   chat's title instead of the branch name." Not "Refactored notification
   title logic" and not "We updated notifications so that...".
3. **Start with where or when** if the change is limited to one place or
   condition: "In cloud workspaces, ...", "When your PR is part of a GitHub
   stack, ...".
4. **Use the imperative for new actions**: "Drag workspaces to reorder them
   in the Pinned section." "Hover over file names in the Git panel to see
   their full paths."
5. **Name UI exactly as the app labels it**, with its capitalization. Write
   settings paths with arrows (Settings → Git → Automatically rename new
   branches), and shortcuts with glyphs (⌘⇧F). Put file names, commands, and
   config keys in backticks.
6. **Tell users how to reach gated or reversible things.** For experiments:
   "Turn on Pokes in Settings → Experimental to ...". For changed defaults:
   "To turn this off, go to ...".
7. **Write fixes as "Fixed <the symptom> <when it happened>."** "Fixed the
   send button staying disabled after pasting or attaching a file." Quote the
   exact error text users saw. Describe the symptom, not the cause. If the
   fix reads better as the new behavior, write that instead: "X no longer
   ...", "X now ...".
8. **Be specific.** "Fixed several issues with the MCP panel" tells the
   reader nothing. Name the issue, or leave the bullet out.
9. **Keep internals out**: no PR numbers, ticket IDs, class or service
   names, or analytics vendors. Use the user's words for features, not the
   team's code names.
10. **State security fixes plainly**, without exploit details: "Agents in
    repositories hosted outside github.com no longer receive your Conductor
    GitHub credentials."
11. **Removals say what replaces them**: "Removed the Team page. Teammate
    profiles replace it."
12. **Credit outside help** inline: "(thanks [@handle](link))".

## Voice

- First person plural, direct, and brief. Light humor is fine in a title or
  lead ("A (mostly) boring update", "We no longer burn your CPU
  (hopefully)"). Keep it out of bullets.
- Say what you know: "We don't know if it's better yet but it's cool" is
  more credible than hype. Apologize briefly when a bug hurt people.
- Use an exclamation mark for a real launch ("Conductor for iOS!"), not for
  every bullet.

## References

Read the one that matches the release:

| Case | Reference |
|---|---|
| New feature, launch, partnership, or changed default | `references/feature-release.md` |
| Mostly fixes and small improvements, no headline | `references/polish-release.md` |
| One change: new model or integration, small feature, hotfix, or a patch after a bigger release | `references/small-release.md` |
| Any release, and rewriting or cleaning up existing bullets | `references/bullets.md` |

## Checklist

- The title names the headline change, or is an honest label for a polish
  release.
- The lead says what the user can now do and how to get to it.
- Every bullet describes something a user sees, with a concrete place,
  action, or symptom.
- Each fix names the symptom. None says only "Fixed an issue" or "Fixed bugs".
- No PR numbers, internal names, or invented behavior.
- UI names, settings paths, and shortcuts match the product.
- Anything users must act on is in the Heads-up or Breaking changes, with
  what to do instead, not hidden in Misc.
- Sections in order: headline, Heads-up, Improvements, Fixes, Misc, New in
  x.y.z. Empty sections are gone.
- Punctuation and tense are the same in all bullets.
