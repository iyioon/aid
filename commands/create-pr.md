---
description: Create a pull request for completed work
agent: dispatch
subtask: false
---

## Create Pull Request

You've completed the work and reviewed it. Now create a pull request.

### Step 1: Ensure All Changes Are Committed

Run `git status` to verify there are no uncommitted changes.
If there are uncommitted changes, commit them first.

### Step 2: Push the Branch

Push your branch to the remote:
```bash
git push -u origin HEAD
```

### Step 3: Gather PR Information

1. Get the list of commits:
   ```bash
   git log --oneline origin/main..HEAD
   ```

2. Get the full diff summary:
   ```bash
   git diff --stat origin/main..HEAD
   ```

### Step 4: Create the Pull Request

Use the GitHub CLI to create a PR. Construct a proper title and body:

```bash
gh pr create --title "TITLE" --body "BODY"
```

**Title Guidelines:**
- Start with type: `feat:`, `fix:`, `docs:`, `refactor:`
- Be concise but descriptive
- Example: `feat: add dark mode toggle to settings`

**Body Template:**
```markdown
## Summary
[Brief description of what this PR accomplishes]

## Changes
- [List specific changes made]
- [Another change]

## Testing
[How the changes were tested, or "Manual testing" if no automated tests]

## Related Issues
[Closes #123 or "N/A" if no related issue]
```

### Step 5: Confirm Success

After creating the PR, output the PR URL so the user can review it.

If the PR creation fails, diagnose the issue and retry.
