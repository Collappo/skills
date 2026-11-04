---
name: more-commits
description: Commit every finished, self-contained change right away with git, one commit per unit of work, so the project keeps a granular, readable history. Use automatically, without being asked, whenever working in a git repository and a change is finished - a feature, a bug fix, a refactor, or a docs, config, style or asset update. Especially use for tasks with several parts (several bugs, tabs, pages, screens, components, review comments, or a feature built in stages), where each part gets its own commit. Also use when the user says "commit", "zacommituj", "more commits", or asks for small, atomic or separate commits. Adapts message style and checks to the repo. Does not push, amend or rewrite history.
---

# More commits

Don't wait to be asked. As soon as one unit of work is finished and works, commit it, before starting the next one. This skill being active is the user's standing request to commit, so don't ask before each commit. Never `git push`, `--amend`, `rebase`, `reset --hard` or force anything unless the user asks.

## What is one commit

One commit = one thing the user would name separately.

| Request | Commits |
|---|---|
| "Fix the Settings, Profile and Billing tabs" | 3: `fix(settings): ...`, `fix(profile): ...`, `fix(billing): ...` |
| "Fix these 4 bugs" | 4, one per bug |
| "Add CSV export" | one per stage that works on its own: `feat(api): add CSV export endpoint`, then `feat(reports): add export button` |
| "Rename X and fix bug Y" | 2: the `refactor` first, then the `fix` |
| One small fix | 1 |

- Keep together what belongs together: a fix and its test, a dependency bump and its lockfile, a rename and all its call sites.
- Split apart what is independent: refactor vs. behaviour change, formatting vs. logic, unrelated fixes even in the same file.
- A shared prerequisite (e.g. a helper two tabs need) gets its own commit, before the units that use it.
- Don't go finer: no commit per edited line, no commit of work that doesn't build.

## Adapt to the repo (once, before the first commit)

```bash
git status --short      # pre-existing changes belong to the user: never stage them
git log --oneline -15   # message convention
git rev-parse HEAD      # starting point for the final summary
```

- **Message style**: copy what `git log` shows - language, Conventional Commits or not, scopes in use, ticket prefixes (`ABC-123: ...`), capitalisation. Also obey a commitlint config (`commitlint.config.*`, `.commitlintrc*`) or a `.gitmessage` template if present. No history or no clear pattern: Conventional Commits in English, `type(scope): summary`, imperative, lower case, max ~72 characters.
- **Check commands**: take them from the project, not from habit - CLAUDE.md, README or CONTRIBUTING, the scripts in `package.json` (run with the package manager its lockfile implies), `Makefile`/`justfile` targets, `pyproject.toml`, and what CI runs (`.github/workflows/`, `.gitlab-ci.yml`). Pick the fastest ones that cover the change: type-check, lint, the related tests. Nothing found: at least re-read the diff.
- **Hooks**: husky, lefthook or `.pre-commit-config.yaml` run on every commit; a failing hook is feedback to act on, not an obstacle.
- **Branch**: commit on the current branch. Create a branch only if the user, the project docs or the session instructions require it, and then only once, at the start.
- **Not a git repo**: ask whether to run `git init`; don't init silently.
- **Pre-existing changes in files you must edit**: tell the user before going on.

## Steps for each unit

1. **Check it works** with the commands found above. Fix errors first. If something can't be fixed, don't commit; tell the user why.
2. **Look at what changed**: `git status --short` and `git diff`.
3. **Stage only this unit's files**, by path (quote paths with spaces), then confirm with `git diff --cached --stat`.
   - Never `git add -A`, `git add .` or `git commit -a`.
   - Never stage build output, dependency folders (`node_modules/`, `.venv/`) or secrets (`.env`, keys).
   - Two units mixed in one file: write `git diff <file>` to a temp patch, delete the other unit's hunks, then `git apply --cached <patch>`. Both in the same hunk: commit them together and say so in the message.
4. **Write the message** in the repo's style. The subject names this unit only (`fix(billing): show VAT in invoice summary`, not `fix tabs`). Add a body when the why isn't obvious from the diff. End with the attribution trailer the session requires, if any.
5. **Commit** so multi-line messages and quotes survive the shell:
   - bash: `git commit -F - <<'EOF'` ... `EOF`
   - PowerShell: write the message to a temp file outside the repo and run `git commit -F <file>` (piping from Windows PowerShell 5.1 can prepend a BOM to the subject or turn non-ASCII characters into `?`).

Also commit before switching to a different task and before ending a turn that changed files.

## When something goes wrong

- **A hook fails** (pre-commit checks, or commit-msg/commitlint rejecting the message): the commit did not happen. Fix the cause in the code or the message, re-stage (the hook may have reformatted files) and commit again. Never `--amend` here - it would rewrite the previous, unrelated commit. Never `--no-verify`.
- **Bug found in an earlier unit**: fix it in a new commit.
- **A unit can't be finished**: don't commit it; keep its edits out of later commits and tell the user.

## Final summary

At the end of the task, list its commits with `git log --oneline <start-sha>..HEAD` - one line per unit - and name anything left uncommitted: unfinished units and the user's own pre-existing changes.
