---
name: git-messages
description: Write git commit messages and pull request (PR) descriptions. Use this whenever the user asks to write, draft, generate, or improve a commit message, a PR description/summary, or asks "what should I put in the commit message" or "write a PR for this". Also trigger after the user has made or described a set of code changes (a diff, a set of files edited, "git diff", "git status", a feature/bugfix they just finished) and asks for help committing or opening a PR. Applies to any repo unless the user says otherwise.
---

# Git Commit & PR Messages

Write commit messages in **Conventional Commits** format and PR descriptions in a **Summary / Changes / Testing / Screenshots** format, based on the actual code changes.

## Before writing anything

Always ground the message in the real diff — never invent changes.

1. If a diff isn't already visible in the conversation, get one:
   - `git diff --staged` (for a commit message, staged changes)
   - `git diff main...HEAD` or `git log main..HEAD -p` (for a PR spanning multiple commits)
2. If Claude has no shell/repo access and the user hasn't pasted a diff, ask them to paste `git diff` output or describe the changes concretely — don't guess from a vague description.

## Commit messages (Conventional Commits)

Format:
```
<type>(<optional scope>): <short summary, imperative mood, lowercase, no period>

<optional body: why the change was made, not just what>

<optional footer: BREAKING CHANGE: ..., Fixes #123>
```

**Types**: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`

Rules:
- Subject line ≤ 50 chars if possible, hard cap 72. Imperative mood ("add", not "added"/"adds").
- Scope is optional — the module/area affected, e.g. `feat(auth):`.
- Body wraps at ~72 chars, explains *why*, separated from subject by a blank line. Omit the body for small/obvious changes.
- One logical change per commit message. If the diff clearly bundles unrelated changes, say so and suggest splitting instead of writing one message that papers over it.
- If a change is breaking, add a `BREAKING CHANGE:` footer describing the impact and migration.
- Reference issues in the footer (`Fixes #123`, `Refs #456`) when the user mentions an issue/ticket number — never fabricate one.

## PR descriptions

Format:
```markdown
## Summary
1-3 sentences: what this PR does and why (the motivation/problem, not just a restatement of the diff).

## Changes
- Bullet list of concrete changes, grouped logically if there are many
- Call out any notable tradeoffs, follow-ups, or things reviewers should pay special attention to

## Testing
- How this was verified: tests added/run, manual steps taken, edge cases checked
- If no tests were added and the change warrants them, say so plainly rather than skipping the section

## Screenshots
- Include if the change is visual/UI-facing
- If not applicable, write "N/A" rather than omitting the section
```

Rules:
- Title the PR itself using the same Conventional Commits style as the primary commit type (e.g. `feat(auth): add SSO login`).
- Keep the Summary about *why*, not a re-listing of the Changes bullets.
- Don't claim testing that wasn't described to you — ask or say "not verified" rather than inventing test coverage.
- If the repo has a `.github/PULL_REQUEST_TEMPLATE.md` or similar, check for it and follow its structure instead of the default above, merging in the Summary/Changes/Testing/Screenshots content where it fits.

## Output

- For a single commit message: give the raw message in a code block, ready to paste into `git commit -m` (or `-F` for multi-line).
- For a PR description: give the markdown in a code block, ready to paste into the PR body.
- Don't wrap the whole response in extra commentary — lead with the message/description itself, then note anything the user should double check (e.g. "couldn't verify testing steps — fill those in").
