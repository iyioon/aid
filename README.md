# AI Dispatch for OpenCode

Autonomous AI workflow system for OpenCode that handles tasks from start to PR creation.

## Features

- **GitHub Issue Integration**: Fetch and work on GitHub issues automatically
- **Plain Text Tasks**: Work on any task described in plain text
- **Git Worktree Isolation**: Each task runs in its own worktree
- **Automatic Cleanup**: Graceful cleanup on exit, including unexpected closures
- **Session Management**: Track, list, and resume dispatch sessions

## Quick Start

```bash
# Work on a GitHub issue
ai-dispatch https://github.com/user/repo/issues/123

# Work on a plain text task
ai-dispatch "Add dark mode toggle to settings page"

# List active sessions
ai-dispatch list

# Clean up orphaned sessions
ai-dispatch cleanup --force
```

## Documentation

- [Installation Guide](docs/installation.md)
- [Usage Guide](docs/usage.md)
- [Configuration](docs/configuration.md)
- [Troubleshooting](docs/troubleshooting.md)

## How It Works

1. **Parse Input**: Detects GitHub issue URL or plain text task
2. **Create Worktree**: Sets up isolated git worktree with `ai/` prefixed branch
3. **Run OpenCode**: Launches OpenCode with dispatch agent and task prompt
4. **Autonomous Work**: Agent implements, commits, reviews, and creates PR
5. **Cleanup**: Removes worktree and cleans state on completion

## Requirements

- `git` (with worktree support)
- `gh` (GitHub CLI, for issue fetching)
- `opencode`
- `jq`

## Directory Structure

```
~/.config/opencode/
├── scripts/
│   └── ai-dispatch.sh      # Main dispatch script
├── agents/
│   └── dispatch.md         # Autonomous dispatch agent
├── commands/
│   ├── work-task.md        # Start working on task
│   ├── review-work.md      # Self-review changes
│   └── create-pr.md        # Create pull request
├── dispatch/               # Session state files
├── worktrees/              # Git worktrees for tasks
└── docs/                   # Documentation
```

## License

MIT
