# Usage Guide

## Basic Usage

### Working on a GitHub Issue

```bash
# Navigate to your project
cd /path/to/your/project

# Dispatch with a GitHub issue URL
ai-dispatch https://github.com/owner/repo/issues/123
```

The agent will:
1. Fetch the issue title and description
2. Create a branch named `ai/issue-123`
3. Set up a worktree in `~/.config/opencode/worktrees/ai-issue-123`
4. Work on the task autonomously
5. Create commits as it progresses
6. Self-review the changes
7. Create a PR against the main branch

### Working on a Plain Text Task

```bash
# Describe what you want done
ai-dispatch "Add a dark mode toggle to the settings page"

# More detailed descriptions work better
ai-dispatch "Refactor the user authentication module to use JWT tokens instead of session cookies. Update all related tests."
```

## Managing Sessions

### List Active Sessions

```bash
ai-dispatch list
```

Output:
```
Active AI Dispatch Sessions
─────────────────────────────────────────────────────────────────
SESSION              STATUS          BRANCH                          CREATED
─────────────────────────────────────────────────────────────────
20250312-143022-1234 running         ai/issue-123                    2025-03-12T14:30:22Z
20250312-150145-5678 completed       ai/task-add-dark-mode-150145    2025-03-12T15:01:45Z
─────────────────────────────────────────────────────────────────
Total: 2 session(s)
```

### Resume a Session

If you need to continue work on a previous session:

```bash
ai-dispatch resume 20250312-143022-1234
```

This opens OpenCode in the existing worktree with conversation history.

### Clean Up Orphaned Sessions

If sessions weren't cleaned up properly (e.g., system crash):

```bash
# See orphaned sessions
ai-dispatch cleanup

# Force cleanup
ai-dispatch cleanup --force
```

## Environment Variables

| Variable | Description |
|----------|-------------|
| `AI_DISPATCH_DEBUG=1` | Enable verbose debug output |
| `AI_DISPATCH_DRY_RUN=1` | Show what would happen without executing |

Example:
```bash
AI_DISPATCH_DEBUG=1 ai-dispatch "Add feature X"
```

## Workflow Details

### What Happens During Dispatch

1. **Input Parsing**
   - Detects if input is a GitHub URL or plain text
   - For GitHub issues, fetches title, body, and labels via `gh` CLI

2. **Branch Creation**
   - GitHub issues: `ai/issue-<number>`
   - Plain text: `ai/task-<sanitized-description>-<timestamp>`

3. **Worktree Setup**
   - Creates worktree in `~/.config/opencode/worktrees/`
   - Based on latest `origin/main` (or `origin/master`)

4. **State Tracking**
   - Creates JSON state file in `~/.config/opencode/dispatch/`
   - Tracks session ID, branch, worktree path, status

5. **OpenCode Execution**
   - Runs `opencode run --agent dispatch` with task prompt
   - Agent works autonomously through the task

6. **Cleanup**
   - On exit (normal or interrupted), removes worktree
   - Deletes branch if it wasn't pushed
   - Updates/removes state file

### The Dispatch Agent

The dispatch agent is configured for autonomous work:
- Full edit permissions (no approval prompts)
- Full bash access including git and gh commands
- Temperature set to 0.3 for consistent behavior

It follows a structured workflow:
1. Understand the task
2. Plan the implementation
3. Implement incrementally with commits
4. Self-review all changes
5. Create a PR

## Tips for Better Results

### Write Clear Task Descriptions

```bash
# Good - specific and actionable
ai-dispatch "Add input validation to the user registration form. Validate email format, password strength (min 8 chars, 1 uppercase, 1 number), and username uniqueness."

# Less ideal - vague
ai-dispatch "Fix the form"
```

### Provide Context

For complex tasks, include relevant context:

```bash
ai-dispatch "Implement rate limiting for the API endpoints. Use Redis for storage. Follow the pattern established in src/middleware/auth.ts. Limit to 100 requests per minute per IP."
```

### Use GitHub Issues for Complex Tasks

GitHub issues allow for:
- Detailed descriptions with markdown
- Labels for categorization
- Comments for additional context
- Automatic linking in the PR
