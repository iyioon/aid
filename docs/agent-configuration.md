# AI Agent Configuration

This file documents configuration options for AI agents in the dispatch system.

## Agent Behavior Modes

### Output Modes

Agents can operate in different output modes depending on the task:

#### 1. Console Output Mode (Default for `aid` direct invocation)
- Agent prints summary to console
- Used for: Interactive TUI sessions, direct task execution
- Behavior: Results shown directly to user in terminal

#### 2. Comment Posting Mode (Default for `aid review`)
- Agent posts structured comment to GitHub
- Used for: PR reviews, issue analysis, automated feedback
- Behavior: Comment posted via `gh pr review` or `gh issue comment`
- Format: Follows `/docs/ai-comment-guidelines.md`

#### 3. Hybrid Mode
- Agent creates PR/commit AND posts analysis comment
- Used for: Self-review workflows, comprehensive updates
- Behavior: Both actions performed sequentially

## Configuration Options

### In Agent Frontmatter

```yaml
---
description: Agent description
mode: primary
temperature: 0.3
comment_config:
  enabled: true              # Enable comment posting capability
  auto_post: true            # Post automatically vs. draft mode
  format: structured         # structured | minimal | detailed
  include_code_suggestions: true
  max_issues_reported: 10
permission:
  bash:
    "gh pr review*": allow   # Required for comment posting
    "gh issue comment*": allow
    "gh pr comment*": allow
---
```

### Environment Variables

```bash
# Enable comment posting mode
export AID_COMMENT_MODE=true

# Set comment format (structured, minimal, detailed)
export AID_COMMENT_FORMAT=structured

# Enable draft mode (show preview without posting)
export AID_DRAFT_MODE=true

# Enable debug output
export AID_DEBUG=1
```

### Command-Line Flags

```bash
# Review with comment posting (default for review)
aid review <pr-url>
aid review <pr-url> --comment

# Review in draft mode (show what would be posted)
aid review <pr-url> --draft

# Review with minimal format
aid review <pr-url> --format minimal

# Interactive review (preview before posting)
aid review <pr-url> --interactive

# Work on PR and post self-review comment
aid <pr-url> --self-review
```

## Agent-Specific Configurations

### Dispatch Agent (Development)

**Purpose:** Complete development tasks, create commits and PRs

**Default output mode:** Console + PR creation

**Comment posting:** Optional, via `--self-review` flag or when analyzing existing PRs

**Configuration:**
- Can post self-review comments after implementation
- Posts PR description (always)
- Can post analysis comments to issues

### Review Agent (Code Review)

**Purpose:** Analyze PRs and provide feedback

**Default output mode:** Comment posting

**Console output:** Disabled (no meta-commentary allowed)

**Configuration:**
- Always posts exactly one review comment
- No console output except the `gh pr review` command
- Follows strict formatting guidelines

## Comment Format Templates

All agents follow the templates defined in `/docs/ai-comment-guidelines.md`.

### Key Principles

1. **Structured Markdown**: Clear sections with `##` and `###` headings
2. **Professional Tone**: Direct, definitive, evidence-based
3. **Actionable**: Every issue includes a concrete fix
4. **Scannable**: Easy to skim and understand quickly
5. **No Meta-Commentary**: Content only, no explanations of what the agent is doing

### Template Selection

| Scenario | Template |
|----------|----------|
| PR Review with issues | Full review template with severity labels, locations, suggestions |
| PR Review without issues | Minimal approval template |
| Issue analysis | Issue response template |
| PR self-review | Self-review template (agent reviewing own work) |
| Progress update | Update template with status and next steps |

## GitHub CLI Integration

### Required GitHub CLI Commands

Agents use these `gh` commands for comment posting:

```bash
# Post PR review comment
gh pr review <number> --repo <owner/repo> --comment --body "..."

# Post PR comment (non-review)
gh pr comment <number> --repo <owner/repo> --body "..."

# Post issue comment
gh issue comment <number> --repo <owner/repo> --body "..."

# Fetch PR data
gh pr view <number> --repo <owner/repo> --json <fields>

# Fetch issue data  
gh issue view <number> --repo <owner/repo> --json <fields>
```

### Authentication

GitHub CLI must be authenticated:

```bash
# Check auth status
gh auth status

# Login if needed
gh auth login

# Set default repo (optional)
gh repo set-default <owner/repo>
```

## Workflow Examples

### Example 1: Standard PR Review

```bash
# User runs review command
aid review https://github.com/owner/repo/pull/42

# Agent behavior:
# 1. Fetches PR data via gh pr view
# 2. Analyzes diff via gh pr diff
# 3. Structures findings per template
# 4. Posts ONE comment via gh pr review
# 5. Stops immediately (no console output)
```

### Example 2: PR Development with Self-Review

```bash
# User runs dispatch with self-review flag
aid https://github.com/owner/repo/pull/42 --self-review

# Agent behavior:
# 1. Implements changes (commits)
# 2. Pushes changes
# 3. Performs self-review of changes
# 4. Posts self-review comment to PR
# 5. Prints PR URL to console
```

### Example 3: Issue Analysis

```bash
# User asks agent to analyze an issue
aid "analyze issue #42 in owner/repo"

# Agent behavior:
# 1. Fetches issue via gh issue view
# 2. Analyzes issue content and comments
# 3. Structures analysis per template
# 4. Posts comment via gh issue comment
# 5. Confirms comment posted
```

### Example 4: Interactive Review

```bash
# User wants to preview before posting
aid review https://github.com/owner/repo/pull/42 --interactive

# Agent behavior:
# 1. Fetches and analyzes PR
# 2. Structures review comment
# 3. Displays formatted preview to console
# 4. Prompts: "Post this review? (y/n)"
# 5. Posts if confirmed, exits if declined
```

## Best Practices

### For Comment Posting

1. **Always include location**: Use `path/to/file.ext:line` format
2. **Always include code suggestions**: Use ```suggestion blocks for fixes
3. **Be definitive**: "This will cause X" not "This might cause X"
4. **Focus on high-signal issues**: ≥80% confidence threshold
5. **No meta-commentary**: Don't explain what you're doing, just do it
6. **Stop after posting**: One comment, then immediately stop

### For PR Descriptions

1. **Be concise**: 1-2 sentence summary
2. **Be specific**: Reference files/components
3. **Group changes**: Don't list every tiny modification
4. **Focus on "why"**: Purpose over mechanics
5. **Professional tone**: Direct and factual

### For Issue Comments

1. **Address the question directly**: Lead with the answer
2. **Provide context**: Explain relevant background
3. **Include code examples**: Show, don't just tell
4. **Suggest next steps**: Make it actionable
5. **Link to related resources**: Documentation, similar issues, PRs

## Troubleshooting

### Comment not posting

1. Check GitHub CLI authentication: `gh auth status`
2. Verify repo access: `gh repo view <owner/repo>`
3. Check permissions in agent frontmatter
4. Enable debug mode: `export AID_DEBUG=1`

### Comment format issues

1. Verify Markdown syntax (check with preview tool)
2. Ensure code blocks use correct fence syntax (```)
3. Check that heredoc EOF markers are quoted: `<<'EOF'`
4. Test command manually in terminal first

### Agent posting multiple comments

1. Review agent instructions (should say "post ONE comment, then stop")
2. Check for loops or retry logic
3. Verify the agent stops after `gh pr review` command
4. Check logs for duplicate executions

## References

- [AI Comment Guidelines](/docs/ai-comment-guidelines.md) - Comprehensive formatting guide
- [GitHub CLI Documentation](https://cli.github.com/manual/) - Official gh reference
- [GitHub Markdown Guide](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax) - Markdown syntax

---

**Last Updated:** March 2026
