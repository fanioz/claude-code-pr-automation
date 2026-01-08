---
name: pr-automation
description: Automated pull request review workflow. Fetch PR from GitHub, review with specialized agents (code quality, error handling, type safety, tests, documentation), post review comments, ask for approval before fixing issues, iterate until mergeable. Use when user asks to "review PR", "check pull request", "fix PR issues", or similar.
allowed-tools: Read, Grep, Glob, Bash, Task, Skill, Edit, Write, AskUserQuestion
model: claude-sonnet-4-5-20250929
---

# Automated PR Review Workflow

## Objective
Automatically review pull requests using specialized agents, post reviews to GitHub, apply fixes with user approval, and iterate until the PR is ready to merge.

## When to Use
Trigger this skill when the user asks to:
- Review a pull request
- Check PR for issues
- Fix PR problems
- Iterate on PR until mergeable

## Workflow Steps

### 1. Identify Pull Request
Determine which PR to review:
- If PR number provided: Use that PR
- If on branch: Find PR for current branch (`gh pr view --json number`)
- If no PR: Ask user which PR to review

```bash
# Get current branch
git branch --show-current

# Find PR for current branch
gh pr view --json number,headRefName,title,state
```

### 2. Fetch PR Information
```bash
# Get PR details
gh pr view $PR_NUMBER --json number,title,author,headRefName,baseRefName,state,additions,deletions,files

# Get PR diff
gh pr diff $PR_NUMBER
```

Store:
- PR number
- Branch name
- Changed files
- Diff content

### 3. Determine Review Scope
Based on file types, decide which agents to launch:

**Always Run**:
- `code-reviewer` - General code quality and conventions

**Run for Code Files** (.ts, .tsx, .js, .jsx, .py, .go, .rs, etc.):
- `silent-failure-hunter` - Error handling and edge cases
- `type-design-analyzer` - Type safety and design

**Run for Test Files** (.test.ts, .spec.ts, __tests__/, *.test.js, *.spec.js):
- `pr-test-analyzer` - Test coverage and quality

**Run for Documentation** (.md, .mdx, .txt):
- `comment-analyzer` - Comment accuracy and completeness

### 4. Launch Review Agents (Parallel)
Use the Task tool to launch agents simultaneously:

**Launch all applicable agents in one message** for efficiency.

**Agent Prompt Template**:
```
Review PR #${PR_NUMBER} "${TITLE}" by ${AUTHOR}

**Files Changed**: ${FILES_CHANGED}
**Branch**: ${BRANCH_NAME}
**Diff**: [include relevant portions of diff]

Focus on: [agent-specific focus area]

Return findings with:
- Critical issues (confidence >= 90)
- High priority issues (confidence >= 80)
- Medium priority issues (confidence >= 70)
- File locations and line numbers
- Specific code examples for fixes
```

### 5. Aggregate Findings
Collect results from all agents and organize by priority:

**Critical Issues** (Must Fix):
- Issue 1: [description] - [agent-name] (file:line)
- Issue 2: [description] - [agent-name] (file:line)

**High Priority Issues** (Should Fix):
- Issue 1: [description] - [agent-name] (file:line)
- Issue 2: [description] - [agent-name] (file:line)

**Medium Priority Issues** (Nice to Have):
- Issue 1: [description] - [agent-name] (file:line)

**Positive Observations**:
- Strength 1: [description]
- Strength 2: [description]

### 6. Generate Review Comment
Format findings into a comprehensive review:

```markdown
## ⚠️ Review: [APPROVE/REQUEST CHANGES/COMMENT]

# PR #${NUMBER}: ${TITLE}

**Author**: ${AUTHOR}
**Branch**: ${BRANCH}
**Changes**: ${ADDITIONS}+ ${DELETIONS}-

---

## Summary

[2-3 sentence summary of overall assessment]

---

## 🚨 Critical Issues (Must Fix) - ${CRITICAL_COUNT}

### 1. [Title]
- **Agent**: [agent-name]
- **File**: `path/to/file:line`
- **Confidence**: X%

**Issue**:
[Description]

**Fix**:
\`\`\`typescript
[fix code]
\`\`\`

[... repeat for each critical issue]

---

## ⚠️ High Priority Issues (Should Fix) - ${HIGH_COUNT}

### 1. [Title]
- **Agent**: [agent-name]
- **File**: `path/to/file:line`
- **Confidence**: X%

**Issue**:
[Description]

**Suggested Fix**:
[Suggestion]

[... repeat for each high issue]

---

## 🔶 Medium Priority Issues - ${MEDIUM_COUNT}

- **[agent-name]**: [description] (`path/to/file:line`)
[... list all medium issues]

---

## ✅ Positive Observations

- [Strength 1]
- [Strength 2]
[... list all positive observations]

---

## Recommended Action

[approve/request changes/comment]

**Next Steps**:
1. Address critical issues first
2. Fix high priority issues
3. Consider medium priority improvements
4. Re-run review after fixes

---

Reviewed by Claude Code PR Automation
Agents: code-reviewer, silent-failure-hunter, type-design-analyzer, pr-test-analyzer, comment-analyzer
```

### 7. Post Review to GitHub
```bash
# Determine review action
if critical_issues > 0:
  action="--request-changes"
elif high_issues > 0:
  action="--comment"
else:
  action="--approve"

# Post review
gh pr review $PR_NUMBER $action --body "$REVIEW_BODY"
```

### 8. Offer to Fix Issues
If critical or high issues found, use AskUserQuestion tool:

```
Found ${critical_count} critical and ${high_count} high priority issues.

What would you like to do?
- Apply all fixes automatically
- Review each fix before applying
- I'll fix manually
- Skip fixes for now
```

### 9. Apply Fixes (If Approved)
For each issue to fix:

**Simple Fixes** (single line/small change):
1. Use Edit tool to apply fix
2. Show user the change

**Complex Fixes** (multiple lines/refactoring):
1. Ask user to review the proposed fix
2. Get approval before applying
3. Use Write tool for complete file rewrite

**Fix Application Pattern**:
```
1. Read current file: Read({ file_path: "path/to/file" })
2. Apply fix: Edit({ file_path, old_string, new_string })
3. Commit: Bash({ command: "git add path/to/file && git commit -m 'fix: description'" })
```

### 10. Push Changes
```bash
# Push fixes to PR branch
git push origin $BRANCH_NAME
```

### 11. Iterate (Loop)
After pushing fixes:
1. Go back to step 2 (fetch PR info)
2. Re-run review with updated code
3. Check if issues are resolved
4. Continue until:
   - No critical or high issues remain
   - PR is approved
   - User chooses to stop
   - Max iterations reached (5)

### 12. Final Approval
When no critical/high issues remain:
```bash
gh pr review $PR_NUMBER --approve --body "✅ All critical and high priority issues resolved. Ready to merge."
```

Optional: Auto-merge if user approves:
```bash
gh pr merge $PR_NUMBER --merge --delete-branch
```

## Error Handling

**If PR doesn't exist**:
- Inform user and ask for correct PR number

**If agents fail**:
- Log error
- Continue with other agents
- Inform user which agents succeeded/failed

**If git push fails**:
- Ask user to resolve merge conflicts
- Retry after conflicts resolved

**If gh CLI not authenticated**:
- Instruct user to run `gh auth login`
- Pause workflow until authenticated

**If merge conflicts exist**:
- Ask user to resolve conflicts
- Retry after resolution

## Tool Permissions

**Always Allowed** (via allowed-tools):
- Read, Grep, Glob - For reading code
- Bash - For git/gh commands
- Task - For launching agents
- AskUserQuestion - For user interaction
- Edit, Write - For applying fixes
- Skill - For invoking other skills

**Will Ask Permission**:
- Any tools not in allowed-tools list

## Best Practices

1. **Be Transparent**: Always show user what you're doing
2. **Ask Before Destructive Actions**: Get approval for pushes/merges
3. **Provide Context**: Explain why each fix is needed
4. **Iterate Safely**: Max 5 iterations to prevent infinite loops
5. **Respect User Choice**: Allow manual fixing or stopping workflow
6. **Maintain Git History**: Write clear commit messages
7. **Test After Fixes**: Re-run review to verify fixes work
8. **Parallel Execution**: Launch agents in parallel for speed

## Troubleshooting

**Skill not triggering**:
- Check description includes trigger words: "review PR", "pull request", "fix issues"
- Verify skill file is in correct location: `~/.claude/skills/pr-automation/SKILL.md`

**Agents not launching**:
- Verify Task tool is in allowed-tools
- Check agent names are correct (see reference.md)
- Ensure agents are available in the system

**Reviews not posting**:
- Verify gh CLI is authenticated: `gh auth status`
- Check PR number is correct
- Ensure you have write access to the repository

**Fixes not applying**:
- Verify file paths are correct
- Check old_string matches exactly (including whitespace)
- Ensure you're on the correct branch

**Push failing**:
- Check for merge conflicts: `git status`
- Verify remote branch exists: `git branch -r`
- Ensure you have push permissions

**Iteration not working**:
- Check that you're fetching the latest PR info
- Verify fixes were actually pushed
- Ensure max iterations not reached

## Example Usage

**User**: "Review PR 28"
**Claude**: [Identifies PR 28, fetches details, launches agents, posts review, asks to fix]

**User**: "Review this PR and fix any issues"
**Claude**: [Full workflow: review → post → fix → iterate → approve]

**User**: "Check if there are any issues with the current PR"
**Claude**: [Review only, no fixes unless user approves]

**User**: "Review PR 123 and iterate until it's ready"
**Claude**: [Full automated workflow with multiple iterations]

## Iteration Tracking

Keep track of:
- Current iteration number (start at 1, max 5)
- Issues fixed in each iteration
- Remaining critical/high issues
- User feedback and decisions

Display progress:
```
Iteration 1/5:
- Critical issues: 2
- High priority issues: 3
- Fixed: 0

Iteration 2/5:
- Critical issues: 0
- High priority issues: 1
- Fixed: 2 critical

[Continue until done]
```
