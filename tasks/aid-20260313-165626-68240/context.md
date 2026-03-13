# Documentation Audit Notes

## Issues Found

### README.md

1. **Review vs Work table** (line 87): Column header says "Creates Worktree" but the table says `aid review` creates "Yes (detached, for code exploration)" and `aid <pr-url>` says "Yes". That's correct. But note the table also has a "Edits Files" and "Posts Comment" column which accurately matches the code.

2. **"How It Works" section** (lines 158-170): Describes `aid review` as:
   - "Creates a detached-HEAD worktree at the PR's head; fork PRs fall back to the source repo" — correct per `review_pr()`.
   
3. **Agents table** (lines 175-178): Correctly lists dispatch and review agents.

4. **Commands table** (lines 180-188): Correctly lists the four slash commands.

5. **Directory Structure** (lines 199-220): Missing `tasks/` entry in the `~/.config/opencode/` tree:
   ```
   ├── tasks/                  # Persistent task contexts  ← currently mentioned but inside the block
   ```
   The structure includes `tasks/` — this is correct.

6. **Environment Variables** (lines 224-228): Missing `AID_NO_CONTEXT=1`. The README only shows `AID_DEBUG=1` and `AID_DRY_RUN=1`. The script header comment also doesn't mention `AID_NO_CONTEXT`.

### docs/installation.md

1. **Uninstalling section** (lines 94-105): Missing `tasks/` directory in the uninstall list — the tasks context directory would be left behind.

2. **GitHub CLI description** (line 12): Says "for accessing GitHub issues" — should be "for GitHub API access (issues, PRs, reviews)" since it's also used for PR review and posting comments.

### docs/usage.md

1. **"Review vs Dispatch" table** (lines 154-164): Row "Creates worktree" says `aid review` → "No" but the actual code in `review_pr()` DOES create a worktree (a detached-HEAD worktree). The README (line 87) correctly says "Yes (detached, for code exploration)". The usage.md is WRONG here.

2. **"Resume a Session" section** (lines 348-354): Documents `aid resume <session-id>` command. This is in the script (`resume_session()`). Correct.

3. **"TUI Mode" section** (lines 3-34): "Guided experience: Clear instructions and interactive prompts" and "Guided experience" / "Interactive workflow" — slightly misleading since interactive mode just opens OpenCode TUI, not with specific guided prompts. But this is acceptable.

4. **"Workflow Details → What Happens During Review" (line 478)**: States "Worktree Setup (non-fork PRs only)" — correct per code.

### docs/configuration.md

1. **Runtime Prompts section** (lines 68-99): Says dispatch prompt is "around line 841" — actual code has it around line 1145-1165. Line number is a rough guide but still inaccurate.

2. **Review prompt section** (lines 82-99): Says review prompt is "around line 596" — actual is around line 806-863. Again stale line reference.

3. **Agent Temperature** (line 38-49): Correctly says dispatch.md and review.md both default 0.3 with YAML frontmatter. Verified correct.

4. **Agent Bash Permissions** (line 128): For review agent, lists `"gh api repos/*/pulls/*": allow`. The actual agent has `"gh api repos/*/pulls/*": allow`. Matches.

5. **Review Agent Tool Restrictions** (lines 145-158): Correctly documents `tools: edit/write/patch/multiedit: false`.

6. **Tasks Directory** (lines 3-12 in configuration.md): Missing `~/.config/opencode/tasks/` from the Directory Locations table.

## Summary of Changes Needed

### README.md
- Add `AID_NO_CONTEXT=1` to Environment Variables table
- Script comment header also lacks `AID_NO_CONTEXT` — update header comment

### docs/installation.md
- Update GitHub CLI description (not just "for accessing GitHub issues")
- Add `tasks/` to uninstall instructions

### docs/usage.md
- Fix "Review vs Dispatch" table: `aid review` DOES create a worktree (detached HEAD)

### docs/configuration.md
- Add `~/.config/opencode/tasks/` to Directory Locations table
- Remove/generalize stale line number references (lines 69, 82) — replace "around line 841" / "around line 596" with just the function name
