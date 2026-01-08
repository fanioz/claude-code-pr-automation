# PR Automation Reference

This document provides detailed reference information for the PR Automation skill.

## Review Agents

### code-reviewer
**Purpose**: General code quality, adherence to conventions, best practices

**Subagent Type**: `pr-review-toolkit:code-reviewer`

**When to Use**: Always run for every PR

**What it Checks**:
- Code style and formatting
- Architecture and design patterns
- Security vulnerabilities
- Performance issues
- Best practices violations
- Adherence to project guidelines (CLAUDE.md)

**Output Format**:
- Critical issues (confidence 90-100)
- Important issues (confidence 80-89)
- Suggestions (confidence 70-79)
- Positive observations

**Example Prompt**:
```
Review PR #28 "Add Back to Top button" with focus on code quality, design patterns, and adherence to Neo Brutalism style guidelines.

Files changed:
- components/back-to-top.tsx (new)
- app/layout.tsx

Return findings with:
- Critical issues (confidence >= 90)
- Important issues (confidence >= 80)
- Suggestions (confidence >= 70)
- File locations and line numbers
- Specific code examples for fixes
```

---

### silent-failure-hunter
**Purpose**: Find silent failures, missing error handling, edge cases

**Subagent Type**: `pr-review-toolkit:silent-failure-hunter`

**When to Use**: Code files with logic, error handling, or external API calls

**What it Checks**:
- Empty catch blocks
- Missing error handling
- Unhandled promises
- Silent failures
- Edge cases not covered
- Inappropriate fallback behavior

**Output Format**:
- Critical: Silent failures that crash app
- High: Missing error handling
- Medium: Edge cases
- Low: Nice to have improvements

**Example Prompt**:
```
Review PR #28 for error handling, silent failures, and edge cases.

Component: components/back-to-top.tsx
- Check for missing try-catch blocks
- Look for empty catch blocks
- Verify error handling in event handlers
- Check for SSR/hydration issues
- Examine scroll event listener edge cases

Return findings with:
- Critical issues (silent failures, crashes)
- High priority issues (missing error handling)
- Medium priority issues (edge cases)
- File locations and line numbers
- Specific fixes with code examples
```

---

### type-design-analyzer
**Purpose**: Analyze type design, encapsulation, invariants

**Subagent Type**: `pr-review-toolkit:type-design-analyzer`

**When to Use**: TypeScript code with types, interfaces, or new component definitions

**What it Checks**:
- Type encapsulation
- Invariant expression
- Type usefulness
- Type enforcement
- Any types usage
- Props interface design
- Type safety in event handlers

**Output Format**:
- Ratings (1-10) for: Encapsulation, Invariant Expression, Usefulness, Enforcement
- Specific type issues with line numbers
- Improvement suggestions
- Missing props interfaces
- Type invariants not expressed

**Example Prompt**:
```
Review PR #28 for type design and type safety.

Component: components/back-to-top.tsx
- Analyze component props interface (if any)
- Check for any types
- Verify type safety in event handlers
- Examine state typing
- Look for type invariants not expressed

Return:
- Qualitative and quantitative ratings
- Specific type issues with line numbers
- Improvement suggestions
- Props interface recommendations
```

---

### pr-test-analyzer
**Purpose**: Review test coverage quality and completeness

**Subagent Type**: `pr-review-toolkit:pr-test-analyzer`

**When to Use**: Test files or when new functionality lacks tests

**What it Checks**:
- Test coverage for new code
- Edge cases tested
- Test quality and clarity
- Missing test cases
- Test organization
- Behavioral test coverage

**Output Format**:
- Critical: No tests for critical functionality
- High: Missing edge cases
- Medium: Low coverage
- Suggestions: Test improvements

**Example Prompt**:
```
Review PR #28 for test coverage.

New functionality: BackToTop component
- Check if tests exist
- Verify edge cases are tested
- Examine test quality
- Identify missing test cases

Return:
- Critical gaps (no tests for critical functionality)
- High priority gaps (missing edge cases)
- Medium priority gaps (low coverage)
- Specific test recommendations
```

---

### comment-analyzer
**Purpose**: Verify comment accuracy and completeness

**Subagent Type**: `pr-review-toolkit:comment-analyzer`

**When to Use**: Documentation files, code with comments, or README files

**What it Checks**:
- Comment accuracy vs code
- Outdated comments
- Missing documentation
- Comment clarity
- Over-commented code
- Comment rot

**Output Format**:
- Critical: Misleading comments
- High: Outdated documentation
- Medium: Missing important comments
- Suggestions: Improvements

**Example Prompt**:
```
Review PR #29 for comment accuracy and documentation quality.

Files:
- content/blog/typescript-advanced-patterns.mdx
- .jules/index.md

Return:
- Critical issues (misleading comments)
- High priority issues (outdated docs)
- Medium priority issues (missing important comments)
- Specific improvements needed
```

---

## Issue Prioritization

### Critical (90-100 confidence)
**Must fix before merge**
- Security vulnerabilities
- Crashes/exceptions
- Breaking changes
- Missing error handling
- SSR/hydration issues
- Type safety violations

### High (80-89 confidence)
**Should fix**
- Performance issues
- Best practices violations
- Type design issues
- Missing tests for critical functionality
- Accessibility issues

### Medium (70-79 confidence)
**Nice to have**
- Code style inconsistencies
- Documentation improvements
- Minor optimizations
- Missing non-critical tests

### Low (<70 confidence)
**Optional**
- Personal preferences
- Minor suggestions
- Nice-to-have improvements

---

## GitHub Integration

### gh PR Commands

**View PR**:
```bash
gh pr view $NUMBER
gh pr view $NUMBER --json number,title,state,files,additions,deletions
```

**Get Diff**:
```bash
gh pr diff $NUMBER
gh pr diff $NUMBER --name-only
```

**Post Review**:
```bash
gh pr review $NUMBER --approve --body "Approved!"
gh pr review $NUMBER --request-changes --body "Please fix these issues"
gh pr review $NUMBER --comment --body "Some suggestions"
```

**Merge PR**:
```bash
gh pr merge $NUMBER --merge
gh pr merge $NUMBER --squash
gh pr merge $NUMBER --rebase
```

**Checkout PR**:
```bash
gh pr checkout $NUMBER
```

**List PRs**:
```bash
gh pr list
gh pr list --state open
gh pr list --author fanioz
```

### Review States

- **APPROVE**: PR is good to merge
- **REQUEST_CHANGES**: PR needs fixes before merge
- **COMMENT**: General feedback, neither approve nor request changes

### JSON Output Format

Useful for parsing:
```bash
gh pr view $NUMBER --json number,title,author,headRefName,state,additions,deletions,files
```

Returns:
```json
{
  "number": 28,
  "title": "Add Back to Top button",
  "author": {"login": "fanioz"},
  "headRefName": "feature/back-to-top",
  "state": "OPEN",
  "additions": 62,
  "deletions": 3,
  "files": [
    {"path": "components/back-to-top.tsx", "additions": 57, "deletions": 0}
  ]
}
```

---

## Fix Strategies

### Simple Text Replacement
Use Edit tool for single-line or small multi-line changes.

**When to use**:
- Adding guards to functions
- Fixing typos
- Small logic changes
- Adding imports

**Example**:
```
Edit({
  file_path: "components/back-to-top.tsx",
  old_string: "window.scrollTo({ top: 0, behavior: 'smooth' })",
  new_string: "if (typeof window === 'undefined') return\ntry {\n  window.scrollTo({ top: 0, behavior: 'smooth' })\n} catch (error) {\n  window.scrollTo(0, 0)\n}"
})
```

### File Rewrite
Use Write tool for complex changes affecting many lines.

**When to use**:
- Major refactoring
- Multiple interconnected changes
- Complete file reorganization
- Adding complex logic throughout

**Example**:
```
Write({
  file_path: "components/back-to-top.tsx",
  content: `[complete new file content]`
})
```

### New Files
Use Write tool to create new files.

**Example**:
```
Write({
  file_path: "components/back-to-top.tsx",
  content: `"use client"\n\nexport function BackToTop() { ... }`
})
```

---

## Commit Messages

Follow conventional commits format:

```
<type>: <description>

[optional body]

[optional footer]
```

**Types**:
- `fix`: Bug fix
- `feat`: New feature
- `refactor`: Code refactoring
- `style`: Code style changes (formatting)
- `docs`: Documentation changes
- `test`: Test changes
- `chore`: Build/process changes

**Example**:
```
fix: resolve SSR crash in BackToTop component

Critical: Add window object guard
Critical: Add error handling in scrollToTop
High: Add hydration mismatch prevention
High: Use passive event listener

Addresses code review findings.

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
```

---

## Iteration Workflow

### Standard Flow
1. **Initial Review**: All agents run
   - Find all issues (critical, high, medium)

2. **Fix Round 1**: Critical issues
   - Apply all critical fixes
   - Commit and push

3. **Review Round 2**: Verify fixes, find remaining
   - Check if critical issues resolved
   - Find remaining high priority issues

4. **Fix Round 2**: High priority issues
   - Apply high priority fixes
   - Commit and push

5. **Review Round 3**: Final check
   - Verify all issues resolved
   - Approve PR

### Max Iterations
- Default: 5 iterations
- Prevents infinite loops
- Configurable in skill

### Termination Conditions
- No critical/high issues remain → Approve
- User declines fixes → Stop and notify
- Max iterations reached → Report status
- PR already approved → Skip review
- User manually stops → Respect user choice

---

## Common Patterns

### Checking File Types
```bash
# Get list of changed files
gh pr diff $PR_NUMBER --name-only

# Determine file types
if file ends with .ts, .tsx, .js, .jsx:
  # Code file → run code-reviewer, silent-failure-hunter, type-design-analyzer
elif file contains "test" or "spec":
  # Test file → run pr-test-analyzer
elif file ends with .md, .mdx:
  # Documentation → run comment-analyzer
```

### Aggregating Agent Results
```
Collect from all agents:
1. Critical issues (confidence >= 90)
2. High priority issues (confidence >= 80)
3. Medium priority issues (confidence >= 70)
4. Positive observations

Organize by:
- Priority (critical → high → medium)
- Agent name
- File location
- Line number

Deduplicate:
- If multiple agents find same issue, report once
- Note which agents found it
```

### Generating Review Body
```
Template:
## ⚠️ Review: [STATE]

# PR #${NUMBER}: ${TITLE}

**Author**: ${AUTHOR}
**Branch**: ${BRANCH}

---

## Summary
[Overall assessment]

---

## 🚨 Critical Issues (${COUNT})
[Iterate and display]

---

## ⚠️ High Priority Issues (${COUNT})
[Iterate and display]

---

## 🔶 Medium Priority Issues (${COUNT})
[Iterate and display]

---

## ✅ Positive Observations
[Iterate and display]

---

## Recommended Action
[approve/request changes/comment]
```

---

## Error Recovery

### Agent Launch Failures
If an agent fails to launch:
1. Log error: "Agent X failed to launch"
2. Continue with other agents
3. Inform user: "Launched agents: code-reviewer ✓, silent-failure-hunter ✗"
4. Proceed with results from successful agents

### Git Push Failures
If `git push` fails:
1. Check for merge conflicts: `git status`
2. If conflicts: Ask user to resolve
3. If permission denied: Check authentication
4. If branch doesn't exist: Ask user to verify
5. Retry after resolution

### PR Not Found
If PR doesn't exist:
1. Verify PR number
2. Check if you're on the correct repo
3. List available PRs: `gh pr list`
4. Ask user for correct PR number

### gh CLI Not Authenticated
If gh commands fail:
1. Check: `gh auth status`
2. If not authenticated: `gh auth login`
3. Pause workflow
4. Resume after authentication

---

## Performance Tips

1. **Launch Agents in Parallel**: Send all agent launches in one message
2. **Fetch PR Info Once**: Store and reuse PR details
3. **Reuse Diffs**: Don't fetch diff multiple times
4. **Limit Iterations**: Max 5 iterations to prevent infinite loops
5. **Early Termination**: Stop if user declines or PR approved

---

## Best Practices

1. **Show Progress**: Always inform user of current step
2. **Ask Permission**: Get approval before pushes/merges
3. **Be Transparent**: Explain what you're doing and why
4. **Provide Context**: Explain why each fix is needed
5. **Respect Choice**: Allow manual fixing or stopping
6. **Maintain History**: Write clear commit messages
7. **Verify Fixes**: Re-run review after applying fixes
8. **Iterate Safely**: Max iterations, clear termination conditions
