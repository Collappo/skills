# Collappo/skills

## Quick Start

```bash
npx skills add Collappo/skills
```

## Skills

<!-- | Skill | What it does |
|---|---|
| [`more-commits`](skills/more-commits/SKILL.md) | Commits every finished unit of work right away, one commit per fix, tab or stage, in the style the repo already uses. | -->

 - #### [`more-commits`](skills/more-commits/SKILL.md)

    Makes the agent commit as it works, instead of leaving one large diff at the end. Each part of a request becomes its own commit:

    | Request | Commits |
    |---|---|
    | "Fix the Settings, Profile and Billing tabs" | 3, one per tab |
    | "Fix these 4 bugs" | 4, one per bug |
    | "Add CSV export" | one per working stage, e.g. the endpoint, then the UI button |
    | One small fix | 1 |

    Before the first commit it adapts to the repository:

    - **Message style** is copied from `git log` (language, Conventional Commits, scopes, ticket prefixes) and any commitlint config. With no history it falls back to Conventional Commits in English.
    - **Checks** come from the project itself: `CLAUDE.md`, `package.json` scripts, `Makefile`, `pyproject.toml` or CI workflows.
    - **Hooks** (husky, lefthook, pre-commit) are respected: a failing hook gets fixed, never skipped.

    It stages files by path only, never commits your own uncommitted changes, and ends the task with a list of the commits it made. It triggers on its own after any finished change, or when you say "commit", "zacommituj" or "more commits".

    The skill does not ask before each commit: installing it counts as permission to commit. It never pushes, amends, rebases or force-resets unless you ask.

    ```bash
    npx skills add Collappo/skills --skill more-commits
    ```

## Structure

```
skills/                    # Skills published by this repo
└── more-commits/
    └── SKILL.md           # Commit workflow: units, repo adaptation, recovery
```

