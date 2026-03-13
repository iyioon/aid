---
description: Create a pull request for completed work
agent: dispatch
---

<instructions>
Create a pull request for the current branch.

1. Verify all changes are committed (`git status`)
2. Push the branch (`git push -u origin HEAD`)
3. Create the PR with `gh pr create`
4. Output the PR URL
</instructions>

<pr_format>
Title: `type: description` (feat, fix, docs, refactor, test, chore)

Body:
```
## Summary
Brief description of what this PR accomplishes.

## Changes
- List of specific changes

## Testing
How changes were verified.
```
</pr_format>

<example>
gh pr create --title "fix: handle null user in auth flow" --body "$(cat <<'EOF'
## Summary
Fixes crash when user object is null during authentication.

## Changes
- Added null check in auth.ts before accessing user properties
- Added test case for null user scenario

## Testing
- Added unit test that passes
- Manually tested login flow
EOF
)"
</example>
