---
name: create-pr
description: Creates a GitHub PR with structured description, updates CHANGELOG.md, bumps version if needed, and commits before opening the PR. Use when the user wants to open a PR or says "create PR", "open PR", "submit PR".
disable-model-invocation: false
---

Create a Pull Request for the current branch. Follow ALL steps in order.

## Step 1 — Understand the changes

Run these commands to understand what's in this branch:
```
git log main..HEAD --oneline
git diff main..HEAD --stat
```

Read the most relevant changed files. Identify:
- What feature or fix was implemented
- Which areas were touched (backend, frontend, etc.)
- The ticket number — look in the **branch name** for patterns like `TICKET-123`, `PD-123`, `ABC-456`
- Whether a version file was already bumped in any commit
- Whether any tests were added or modified in this branch

## Step 2 — Update CHANGELOG.md

If a `CHANGELOG.md` exists, add a new entry at the very TOP, after the `---` divider and before existing entries.

Use this format:
```
## YYYY-MM-DD - [TICKET-NUMBER]
### TYPE
- Description of change in plain english, written for humans.
- Additional bullet if needed.
```

Rules:
- Date = today's date
- TYPE = Added / Changed / Fixed / Security / Deprecated / Removed
- Ticket number = extracted from the branch name. If none found, use [NO-TICKET]
- Keep descriptions concise and human-readable
- Group related bullets under the same ### TYPE heading
- If multiple types apply, use multiple ### headings
- If no CHANGELOG.md exists, skip this step

## Step 3 — Handle tests

Check whether any test/spec files were added or modified in this branch:
```
git diff main..HEAD --name-only | grep -E "spec|test"
```

If **no tests were added or modified**:
- Ask the user: "No tests were found for these changes. Would you like me to generate and run tests before committing?"
- If yes: generate appropriate tests, run them, fix any failures, then include test files in the Step 4 commit
- If no: proceed without tests

## Step 4 — Bump version

Check if a version file exists (common locations: `lib/*/version.rb`, `version.rb`, `package.json`, `pyproject.toml`, `VERSION`).

Check `git log main..HEAD --oneline` — if a version bump commit **already exists** in the branch, skip this step entirely.

If no bump has been made yet:
- **Always ask the user before bumping.** Show the current version and suggest the appropriate increment:
  - Default suggestion: PATCH (e.g. 1.2.3 → 1.2.4)
  - New features: suggest MINOR (1.2.3 → 1.3.0)
  - Breaking changes: suggest MAJOR (1.2.3 → 2.0.0)
- Wait for the user to confirm before writing any changes
- Once confirmed, write the new version to the file

## Step 5 — Commit CHANGELOG, version bump, and tests

Before committing, **show the user a summary and ask for confirmation**:

> "I'm about to make the following commit:
> - 📄 CHANGELOG.md — new entry for [TICKET-NUMBER]
> - 🔢 [version file] — bumped to X.X.X (or "no change")
> - 🧪 [test files if any] — N test(s) generated
>
> Commit message: `chore: update CHANGELOG and bump version for [TICKET-NUMBER]`
>
> Shall I proceed? (yes/no)"

**Wait for explicit confirmation before running any git commands.**

Once confirmed:
```
git add CHANGELOG.md [version file] [test files if any]
git commit -m "chore: update CHANGELOG and bump version for [TICKET-NUMBER]"
```

## Step 6 — Build the PR description

Use this template, filling in every section:

---
## Description
[2-3 sentences describing what this PR does and why. Be specific about the problem solved or feature added.]

## Changes
[Bullet list of meaningful changes grouped by area. Only include areas that actually changed.
Be specific — include function/method names, module names, and examples where relevant.
Also include the test cases covered in the commits.]
- **Backend:** ...
- **Frontend:** ...

## Rollback Plan
[Concrete rollback steps. e.g:
- "Revert this PR and redeploy — no DB changes"
- "Run DB rollback then revert PR — migration adds nullable column only"
- "Feature flag can be disabled via ENV without redeployment"]

## Screenshots
[If UI change: "Screenshots needed — please add before marking ready for review"]
[If backend only: "N/A — no UI changes"]

## How to Test
1. ...
2. ...
3. Expected result: ...

## Related Issues
Closes $ARGUMENTS
---

## Step 7 — Create the PR as draft

Determine the ticket number in this order:
1. Use `$ARGUMENTS` if provided (e.g. `/create-pr TICKET-123`)
2. Otherwise extract from the branch name (e.g. `feature/TICKET-123-description` → `TICKET-123`)
3. If neither found, use `NO-TICKET`

Before creating the PR, **show the user a preview and ask for confirmation**:

> "I'm about to open a draft PR with the following:
> - 🏷  Title: `[TICKET-123] short description`
> - 🌿 Branch: `current-branch` → `main`
> - 📝 Description: [full description from Step 6]
>
> Shall I create this draft PR? (yes/no)"

**Wait for explicit confirmation before running `gh pr create`.**

Once confirmed:
```
gh pr create \
  --title "[TICKET-NUMBER] short description of what was done" \
  --body "[the description from Step 6]" \
  --base main \
  --draft
```

⚠️ CRITICAL: ALWAYS include `--base main` explicitly. Never omit this flag.

Title format: `[TICKET-123] short plain english description`
- Ticket number in square brackets first
- Followed by a plain english, lowercase description
- No type prefix, no colon — just ticket + description
- Always create as **draft** so the user reviews before marking ready

## Step 8 — Output summary

Print:
- ✅ CHANGELOG entry added (or "no CHANGELOG found, skipped")
- ✅ Version bumped: X.X.X → X.X.X (or "already bumped" or "skipped")
- ✅ Tests: generated and passing (or "existing tests found" or "skipped by user")
- ✅ Commit created: [commit hash]
- ✅ PR opened: [PR URL]
- ⚠️  Please review draft PR and mark ready when done
