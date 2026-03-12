# AI-Generated Comment Guidelines

This document provides best practices for crafting professional, clean, and accurate AI-generated comments for PRs, issues, and reviews.

## Table of Contents

1. [Core Principles](#core-principles)
2. [Comment Structure](#comment-structure)
3. [Prompt Engineering Best Practices](#prompt-engineering-best-practices)
4. [Output Formatting Standards](#output-formatting-standards)
5. [Examples](#examples)

## Core Principles

### 1. **Be Concise and Actionable**
- Get to the point quickly
- Focus on what needs to be done
- Avoid verbose explanations unless necessary
- Every comment should have clear next steps

### 2. **Use Structured Formatting**
- Leverage Markdown for clarity
- Use sections (##) to organize content
- Use lists for multiple items
- Use code blocks with syntax highlighting
- Use tables for comparisons

### 3. **Maintain Professional Tone**
- Be direct but respectful
- Avoid conversational filler ("I think", "maybe", "possibly")
- Use declarative statements
- No emojis unless the project culture embraces them
- Avoid excessive enthusiasm or praise

### 4. **Provide Context and Evidence**
- Reference specific files and line numbers
- Quote relevant code snippets
- Link to related issues or documentation
- Cite sources when applicable

### 5. **Be Deterministic**
- Provide concrete suggestions, not vague advice
- Include specific code examples for fixes
- Use definitive language ("This will cause X" vs "This might cause X")
- Back up claims with evidence

## Comment Structure

### Standard Comment Template

```markdown
## [Section Title]

[Brief summary in 1-2 sentences]

### [Subsection if needed]

[Detailed content]

**Key Points:**
- Point 1
- Point 2
- Point 3

**Recommendation:**
[Specific action to take]
```

### PR Review Comment Template

```markdown
## Code Review

### Summary
[1-2 sentence overview of review findings]

### Issues Found

#### [Severity] - [Issue Title]
**Location:** `path/to/file.ext:line`

**Issue:**
[Description of the problem]

**Impact:**
[What will happen if not fixed]

**Recommendation:**
```suggestion
[Corrected code here]
```

[Optional: Additional context or explanation]

---

### Verdict
**[Approve | Request Changes | Comment]**

[Optional: Brief justification if requesting changes]
```

### Issue Comment Template

```markdown
## [Response Type: Analysis | Update | Question]

[Brief response to the issue]

### [Relevant Section Title]

[Detailed information]

**Next Steps:**
1. Step one
2. Step two
3. Step three

[Optional: References or links]
```

## Prompt Engineering Best Practices

Based on research from GitHub, OpenAI, and Anthropic, here are the key principles:

### 1. **Set Clear Context**
```markdown
You are analyzing code changes in a production web application.
Your audience is experienced developers.
Focus on security, performance, and maintainability.
```

### 2. **Define Output Constraints**
```markdown
Post exactly ONE comment using the following structure:
- Do not include meta-commentary
- Do not explain what you're doing
- Use the exact format specified
- Stop immediately after posting
```

### 3. **Specify Tone and Style**
```markdown
Tone: Professional, direct, evidence-based
Style: Structured with clear sections, use Markdown
Language: Declarative statements, no hedging
Format: GitHub-flavored Markdown with code blocks
```

### 4. **Provide Examples**
Include 1-2 examples of desired output format:

```markdown
Example of a good review comment:

## Code Review

### Issues Found

#### High - SQL Injection Vulnerability
**Location:** `api/user.js:45`

**Issue:**
User input is directly concatenated into SQL query without sanitization.

**Impact:**
Attackers can execute arbitrary SQL commands, potentially accessing or deleting data.

**Recommendation:**
```suggestion
const query = 'SELECT * FROM users WHERE email = ?';
db.query(query, [userEmail], callback);
```

Use parameterized queries to prevent SQL injection.
```

### 5. **Define Severity Thresholds**

| Severity | Criteria | Example |
|----------|----------|---------|
| **Critical** | Production failure, security breach, data loss | SQL injection, memory leak, authentication bypass |
| **High** | Significant bugs, security concerns, broken features | Null pointer errors, race conditions, XSS vulnerabilities |
| **Medium** | Best practice violations, maintainability issues, technical debt | Missing error handling, inefficient algorithms, code duplication |
| **Low** | Style issues, minor optimizations, documentation | Variable naming, comment clarity, minor refactoring |

### 6. **Use Confidence Thresholds**
- Only report issues with ≥80% confidence
- If uncertain, phrase as a question: "Should this handle the case where X?"
- Clearly distinguish between bugs (definite issues) and suggestions (improvements)

### 7. **Focus on the Diff**
```markdown
IMPORTANT: Only review code that appears in the diff.
Do not comment on pre-existing code unless it's directly affected by the changes.
Do not suggest refactoring unrelated code.
```

## Output Formatting Standards

### Markdown Best Practices

1. **Headings**: Use `##` for main sections, `###` for subsections
2. **Code blocks**: Always specify language for syntax highlighting
   ````markdown
   ```javascript
   const example = "code";
   ```
   ````
3. **Lists**: Use `-` for unordered, `1.` for ordered
4. **Emphasis**: Use `**bold**` for important points, `*italic*` sparingly
5. **Links**: Use descriptive text: `[see documentation](url)` not `[click here](url)`
6. **Tables**: Use for structured comparisons
7. **Horizontal rules**: Use `---` to separate major sections

### Code Suggestion Format

GitHub supports inline code suggestions in PR comments:

````markdown
```suggestion
corrected code here
```
````

This allows reviewers to apply suggestions with one click.

### File and Line References

Always use this format for clarity:
```
`path/to/file.ext:line_number`
```

Example: `src/components/Auth.tsx:45`

## Examples

### Example 1: Security Review Comment

```markdown
## Security Review

### Critical Issues

#### Critical - Exposed API Keys
**Location:** `config/database.js:12`

**Issue:**
Database credentials are hardcoded in the source file.

**Impact:**
Anyone with repository access can see production credentials. This poses a severe security risk.

**Recommendation:**
```suggestion
const dbConfig = {
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
};
```

Move credentials to environment variables and add `.env` to `.gitignore`.

---

### Verdict
**Request Changes**

Critical security issues must be resolved before merging.
```

### Example 2: Code Quality Review

```markdown
## Code Review

### Issues Found

#### Medium - Missing Error Handling
**Location:** `api/handlers/user.ts:23`

**Issue:**
Async function lacks try-catch block for error handling.

**Impact:**
Unhandled promise rejections may crash the application.

**Recommendation:**
```suggestion
async function getUser(id: string) {
  try {
    const user = await database.users.findOne({ id });
    return user;
  } catch (error) {
    logger.error('Failed to fetch user:', error);
    throw new Error('User retrieval failed');
  }
}
```

---

#### Medium - Inefficient Database Query
**Location:** `api/handlers/posts.ts:56`

**Issue:**
N+1 query problem - fetching users in a loop for each post.

**Impact:**
Performance degradation with large datasets (O(n) database calls).

**Recommendation:**
```suggestion
const posts = await database.posts.find();
const userIds = [...new Set(posts.map(p => p.userId))];
const users = await database.users.find({ id: { $in: userIds } });
const userMap = new Map(users.map(u => [u.id, u]));
posts.forEach(p => p.author = userMap.get(p.userId));
```

Fetch all users in a single query and build a lookup map.

---

### Verdict
**Request Changes**

Address error handling and performance issues before merging.
```

### Example 3: Approval with Minor Suggestions

```markdown
## Code Review

### Minor Suggestions

#### Low - Consider Adding Type Safety
**Location:** `utils/format.ts:15`

**Suggestion:**
Consider adding explicit return type annotation for better IDE support:

```suggestion
export function formatDate(date: Date): string {
  return date.toISOString().split('T')[0];
}
```

---

### Verdict
**Approve**

Code is solid. Minor suggestion is optional.
```

### Example 4: Issue Response

```markdown
## Analysis

This issue is related to rate limiting on the API endpoint. Here's what I found:

### Root Cause

The rate limiter is configured with a 10-request per minute limit per IP address, but the frontend makes 15 requests during the initial page load.

**Affected Code:** `middleware/rateLimit.js:8`

### Proposed Solution

1. Increase rate limit to 20 requests per minute for authenticated users
2. Implement request batching on the frontend to reduce total requests
3. Add rate limit headers to API responses for better client-side handling

```javascript
// Recommended configuration
const limiter = rateLimit({
  windowMs: 60 * 1000,
  max: req.user ? 20 : 10, // Higher limit for authenticated users
  standardHeaders: true,
  legacyHeaders: false,
});
```

### Next Steps

1. Update rate limiter configuration
2. Modify frontend to batch requests where possible
3. Add rate limit monitoring to dashboards

Would you like me to create a PR with these changes?
```

## Configuration Options

When implementing AI comment functionality, consider these configuration options:

### Agent Configuration

```yaml
# In agent frontmatter
comment_mode:
  enabled: true              # Enable comment posting
  auto_post: true            # Automatically post (vs draft mode)
  format: structured         # structured | minimal | detailed
  include_severity: true     # Include severity labels
  include_suggestions: true  # Include code suggestions
  max_issues: 10            # Max issues to report
```

### Command-Line Options

```bash
# Review with comment posting
aid review <pr-url> --comment

# Review with draft mode (don't post)
aid review <pr-url> --draft

# Review with minimal output
aid review <pr-url> --format minimal

# Interactive review (show preview before posting)
aid review <pr-url> --interactive
```

## Anti-Patterns to Avoid

### ❌ Don't: Be Conversational
```markdown
Hey there! I noticed that you might want to consider maybe adding some
error handling here. Just a thought! 😊
```

### ✅ Do: Be Direct
```markdown
**Medium - Missing Error Handling**
Add try-catch block to handle potential database connection failures.
```

---

### ❌ Don't: Provide Vague Feedback
```markdown
This code could be better. Consider refactoring.
```

### ✅ Do: Be Specific
```markdown
**Location:** `utils/calc.ts:45`
**Issue:** Nested loops create O(n²) complexity.
**Recommendation:** Use a Map for O(n) lookup instead.
```

---

### ❌ Don't: Include Meta-Commentary
```markdown
I'm going to review this PR now. Let me check the changes...
After analyzing the code, here's what I found...
```

### ✅ Do: Go Straight to Content
```markdown
## Code Review

### Issues Found
[directly show the issues]
```

---

### ❌ Don't: Use Uncertain Language
```markdown
This might potentially cause issues in some cases possibly.
```

### ✅ Do: Use Definitive Language
```markdown
This will cause a null pointer exception when user is undefined.
```

## Quality Checklist

Before posting an AI-generated comment, verify:

- [ ] Uses structured Markdown with clear sections
- [ ] Includes specific file paths and line numbers
- [ ] Provides concrete code examples or suggestions
- [ ] Uses professional, direct language
- [ ] Focuses only on the diff (not pre-existing code)
- [ ] Severity labels are accurate and justified
- [ ] Each issue includes a clear recommendation
- [ ] No meta-commentary or explanations of process
- [ ] Proper code formatting with syntax highlighting
- [ ] Actionable verdict or next steps

## References

- [GitHub Blog: How to Write Better Prompts for GitHub Copilot](https://github.blog/developer-skills/github/how-to-write-better-prompts-for-github-copilot/)
- [OpenAI: Structured Outputs in the API](https://openai.com/index/introducing-structured-outputs-in-the-api/)
- [GitHub Docs: Pull Request Review Comments](https://docs.github.com/en/rest/pulls/comments)
- [Anthropic: Prompt Engineering Techniques](https://docs.anthropic.com/claude/docs/prompt-engineering)

---

**Last Updated:** March 2026
