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
[1-2 sentence description of what this PR accomplishes]

## Changes
- [Specific change with file reference if helpful]
- [Another specific change]
- [One more change]

## Testing
[How tested: unit tests, integration tests, manual testing, etc.]

## Related Issues
[Closes #123 | Fixes #456 | Related to #789 | N/A]
```

**Important:** Follow the PR description best practices in the dispatch agent instructions for professional, scannable PR descriptions.

### Step 5: Confirm Success

After creating the PR, output the PR URL so the user can review it.

If the PR creation fails, diagnose the issue and retry.
