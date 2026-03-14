---
name: code-review
description: Perform structured code reviews on local changes or remote PRs. Analyzes correctness, security, performance, maintainability, and more. Supports goal-oriented reviews when a plan/spec is provided. Use when reviewing code, PRs, diffs, or when the user mentions code review, review this, or review PR.
---

# Code Review

## 1. Determine Review Target

- **Remote PR**: If user provides PR number/URL (e.g., "Review PR #123"), target that PR
- **Local Changes**: Follow the branch detection flow below

### Local Changes — Branch Detection Flow

1. Get current branch: `git rev-parse --abbrev-ref HEAD`
2. Determine base branch (first match wins):
   1. `git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null | sed 's|refs/remotes/origin/||'` — remote default
   2. If no remote: `git show-ref --verify refs/heads/main` → use `main`; else try `master`
   3. If none found → abort, ask user to specify base branch

| Current Branch | What to Review | Commands |
|---------------|----------------|----------|
| **Feature branch** (≠ main/master) | Staged files **+** full branch diff against base | `git diff --cached --name-only` + `git diff main...HEAD --name-only` (dedupe) |
| **main/master** | Staged files only | `git diff --cached --name-only` |
| **main/master, nothing staged** | **Abort** — nothing to review | Inform user: no staged changes on base branch |

## 2. Preparation

### For Remote PRs:
1. Checkout the PR:
   ```bash
   gh pr checkout <PR_NUMBER>
   ```
2. Read PR description and existing comments for context

### For Local Changes:
1. Identify changes using scope commands above
2. Optionally run preflight if changes are substantial
3. Skip generated/vendor files (node_modules, dist, .generated, etc.)

## 3. Determine Review Mode

Before analyzing code, determine the **review mode**:

### Goal-Oriented Review (plan/spec provided)
Triggers when any of these are present:
- User provides a plan file, PRD, spec, or implementation document
- PR description references a plan, feature spec, or milestone list
- User explicitly states the goal the changes should achieve

When triggered:
1. **Read the plan/spec thoroughly** — extract the primary goal, milestones, acceptance criteria, and any non-functional requirements
2. **Goal Alignment Assessment** — for each milestone/criterion, determine:
   - ✅ **Addressed**: code changes satisfy it
   - ⚠️ **Partially addressed**: incomplete or deviates from spec
   - ❌ **Missing**: not addressed at all
   - 🔀 **Diverged**: implementation contradicts or departs from plan
3. **Surface gaps early** — missing or diverged items are top-priority findings, reported before any technical analysis
4. **Then proceed** to the Analysis Pillars (step 4) for the standard technical review

### Technical Review (no plan/spec)
Default mode when no plan or goal context is provided. Proceed directly to the Analysis Pillars (step 4).

## 4. Analysis Pillars

Analyze code changes across these dimensions:

- **Correctness**: Bugs and logic errors (off-by-one, null handling, race conditions, unhandled exceptions, boolean logic, type mismatches, resource leaks)
- **Maintainability**: Clean, well-structured, easy to modify? Good modularity and patterns?
- **Readability**: Well-commented where needed? Consistent with project style?
- **Efficiency**: Any performance bottlenecks or resource inefficiencies?
- **Security**: Any vulnerabilities or insecure practices?
- **Edge Cases**: Proper error handling? Edge cases covered?
- **Testability**: Adequate test coverage? Suggest additional test cases if needed

## 5. Specialist Analysis (Delegate)

Delegate to specialist agents for deeper analysis when relevant code is detected:

| Agent | When to Delegate |
|-------|------------------|
| **refactoring** | Code smells, design patterns, technical debt |
| **security** | Auth, input handling, data exposure, XSS/CSRF/injection risks |
| **performance** | N+1 queries, blocking async, inefficient algorithms, Core Web Vitals |
| **accessibility** | UI components, semantic HTML, ARIA, keyboard navigation |
| **identity** | Auth flows, session handling, OAuth, passkeys |

Include specialist findings in the appropriate severity section (🚨/⚠️/ℹ️).

## 6. Provide Feedback

### Output Format (Grouped by Category)

```markdown
## Code Review: [scope or PR#]

### Summary
**Target**: `branch-name` vs `base-branch` | PR #N | staged on `base-branch`
**Files**: X | **Issues**: 🚨 C, ⚠️ W, ℹ️ I
**Review Mode**: Goal-Oriented (plan: `path/to/plan`) | Technical

[A paragraph summarizing the review]

### Goal Alignment _(goal-oriented mode only)_
**Primary Goal**: [extracted from plan]

| # | Milestone / Criterion | Status | Notes |
|---|----------------------|--------|-------|
| 1 | [milestone] | ✅ Addressed | [brief evidence] |
| 2 | [milestone] | ⚠️ Partial | [what's missing] |
| 3 | [milestone] | ❌ Missing | [not found in changes] |
| 4 | [milestone] | 🔀 Diverged | [how it differs] |

**Verdict**: [Goal achieved / Partially achieved / Not achieved] — [one-line rationale]

### Security
- 🚨 C1: [issue] `filename:line` - [fix]
- ⚠️ W1: [issue] `filename:line` - [suggestion]

### Performance
- ⚠️ W2: [issue] `filename:line` - [suggestion]

### Code Quality
- ⚠️ W3: [issue] `filename:line` - [suggestion]
- ℹ️ I1: [observation] `filename:line`

### Accessibility
- ⚠️ W4: [issue] `filename:line` - [suggestion]

### Identity/Auth
- 🚨 C2: [issue] `filename:line` - [fix]

### Recommendations
[Overall suggestions]
```

**Notes**:
- Only include sections with findings. Omit empty categories.
- **Goal Alignment** section appears first (after Summary) in goal-oriented mode; omit entirely in technical mode.
- Categories not limited to above—add others as needed (e.g., Testing, Documentation, Architecture).

### Tone

- Professional, direct. Code review only—no praise, no softening.
- Explain _why_ a change is requested.
- Be specific: cite exact lines, show code snippets.
- Prioritize: critical bugs > security > logic > style.
- Context-aware: consider project patterns from AGENTS.md.
- Actionable: every issue needs a clear fix path.

## 7. Save Review

Save review to the path provided by the caller. If no path is provided, default to:

`{project_root}/.cursor/tmp/reviews/review-{YYYY-MM-DD}-{n}.md`

- `{n}` = next consecutive number for that date
- Create parent directories if needed
- Apply any user feedback to this file accordingly

**Output only the file creation.** No recap, summary, or repetition of review content.
