# Skills repo

- Root: custom skills, one folder per skill (`<name>/SKILL.md`).
- `3party/`: copies of third-party skills installed on the owner's machine. `npx skills add incpo/skills` does not pick them up; `npx skills add incpo/skills/3party` does.

## Sync `3party/` with the local machine

1. Run `npx skills ls -g`. A skill is third-party when its `Source` is a GitHub repo (anything other than `local` or `incpo/skills`). Upstream sources also live in `~/.agents/.skill-lock.json`.
2. For each third-party skill, replace the copy: `rm -rf 3party/<name> && cp -RL <installed path> 3party/`. `-L` follows symlinks (`~/.claude/skills/*` often links into `~/.agents/skills/*`).
3. Delete folders in `3party/` whose skill is no longer installed.
4. Remove junk: `find . -name .DS_Store -delete`. Skip skill-creator eval folders (`*-workspace`).
5. Verify: `npx skills add ./3party -l` lists every folder, and `npx skills add . -l` lists only root skills.

Do not edit files in `3party/`. Fix upstream or update locally, then sync again.

## Sync custom skills

Custom skills have `Source: local` in `npx skills ls -g` and are not in `~/.agents/.skill-lock.json`. Check all skill dirs: `~/.agents/skills`, `~/.claude/skills`, `~/.codex/skills`. The repo folder name must match the `name:` in `SKILL.md` (e.g. `~/.codex/skills/writing` → `founder-voice-writer/`). Copy them to the repo root the same way. Only add skills the owner asks for.

Before committing, make sure custom skills contain no personal data: no names, emails, usernames, company or product names, home-directory paths, or hardcoded Linear/workspace IDs. Resolve such values at runtime instead (e.g. the Linear user via `get_user("me")`, the project from the repo name).
