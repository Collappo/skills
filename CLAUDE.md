# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A collection of agent skills published on GitHub as `Collappo/skills` and installed with the `skills` CLI:

```bash
npx skills add Collappo/skills                        # pick from all skills
npx skills add Collappo/skills --skill more-commits   # one skill
```

There is no app, build or test suite. The product is `skills/<name>/SKILL.md`.

| Path | Role |
|---|---|
| `skills/<name>/` | Skills this repo publishes. Edit here. |
| `.agents/skills/` | Dev tooling installed by `npx skills` (ponytail from `dietrichgebert/ponytail`, skill-creator from `vercel-labs/json-render`), recorded with hashes in `skills-lock.json`. Upstream copies: do not edit. `skill-creator` is gitignored; restore it with `npx skills experimental_install`. |
| `.claude/skills` | Committed relative symlink to `../.agents/skills`, so Claude Code loads the dev tooling here. On Windows, recreating it needs Developer Mode or admin: `cmd /c mklink /D .claude\skills ..\.agents\skills`. |

## Commands

```bash
# validate one skill (needs PyYAML)
PYTHONUTF8=1 python .agents/skills/skill-creator/scripts/quick_validate.py skills/<name>

# scaffold a new skill
PYTHONUTF8=1 python .agents/skills/skill-creator/scripts/init_skill.py <name> --path skills
```

`PYTHONUTF8=1` is required on Windows: the scripts print emoji and read files with the system code page, so without it they can crash.

## Skill conventions

- `SKILL.md` in English, plain ASCII. The frontmatter has only `name` (kebab-case, same as the folder) and `description`, with no `<` or `>` in the description.
- The description is the only part read before the skill triggers, so every trigger goes there: situations, phrases, and Polish trigger words when users would say them (e.g. "zacommituj").
- Skills must work in any repo. Take commands, message styles and conventions from the target project at run time. Never hard-code one project's commands or names.
- A skill folder holds `SKILL.md` and only the `scripts/`, `references/` or `assets/` it really uses. No README or changelog inside it.
- Every published skill has an entry in `README.md` under `## Skills` (what it does, an example, its `npx skills add ... --skill <name>` line) and a line in the `## Structure` tree.

## When asked for a new skill

1. Load the `skill-creator` skill and follow it. If the purpose or triggers are unclear, ask one or two questions with concrete example requests.
2. Scaffold it with `init_skill.py` into `skills/<name>/` (never into `.agents/skills/` or `.claude/skills/`), then delete the example `scripts/`, `references/` and `assets/` the skill does not need.
3. Write `SKILL.md` following the conventions above. Keep it short: only what a capable model would not know or would get wrong.
4. Validate with `quick_validate.py`.
5. Test it for real before calling it done. Build a small fixture outside the repo (in the scratchpad) that contains the traps the skill must handle. Run a realistic task by following only the `SKILL.md`. Check the result with a small assert script, and run a deliberately wrong version as a negative control that must fail. Report what the test could not cover, such as whether a fresh model triggers the skill.
6. Add the skill to `README.md` (`## Skills` and `## Structure`).
7. Run `package_skill.py` only if the user wants a `.skill` file to upload to claude.ai, and do not commit that file.