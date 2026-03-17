---
description: Execute full task workflow (explore, plan, implement, review, PR)
agent: dispatch
---

<task>
$ARGUMENTS
</task>

<instructions>
Complete this task autonomously through to a pull request.

1. Understand the task by reading relevant code
   - If the task touches unfamiliar APIs/libraries, do lightweight research before implementation:
     - Prefer official docs first, then real-world usage examples
     - Keep research bounded (default: up to 3 doc lookups and up to 3 usage searches)
     - Stop once confidence is sufficient to implement safely
2. Implement the solution, committing as you go
3. Get a code review from @reviewer (fix issues, max 3 cycles)
4. Push and create the PR

Output the PR URL when done. Only stop if genuinely blocked.
</instructions>
