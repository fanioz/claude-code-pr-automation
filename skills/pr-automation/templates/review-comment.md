# Review Comment Template

This template is used by the PR Automation skill to format review comments.

## Template Structure

```markdown
## ⚠️ Review: {{REVIEW_STATE}}

# PR #{{PR_NUMBER}}: {{PR_TITLE}}

**Author**: {{AUTHOR}}
**Branch**: {{BRANCH_NAME}}
**Changes**: {{ADDITIONS}}+ {{DELETIONS}}-

---

## Summary

{{OVERALL_ASSESSMENT}}

---

## 🚨 Critical Issues (Must Fix) - {{CRITICAL_COUNT}}

{{#each critical_issues}}
### {{@index}}. {{title}}
- **Agent**: {{agent_name}}
- **File**: `{{file_path}}:{{line_number}}`
- **Confidence**: {{confidence}}%
- **Impact**: {{impact}}

**Issue**:
{{description}}

**Fix**:
```typescript
{{fix_code}}
```

{{/each}}

---

## ⚠️ High Priority Issues (Should Fix) - {{HIGH_COUNT}}

{{#each high_issues}}
### {{@index}}. {{title}}
- **Agent**: {{agent_name}}
- **File**: `{{file_path}}:{{line_number}}`
- **Confidence**: {{confidence}}%

**Issue**:
{{description}}

**Suggested Fix**:
{{fix_suggestion}}

{{/each}}

---

## 🔶 Medium Priority Issues - {{MEDIUM_COUNT}}

{{#each medium_issues}}
- **{{agent_name}}**: {{description}} ({{file_path}}:{{line_number}})
{{/each}}

---

## ✅ Positive Observations

{{#each positive_observations}}
- {{description}}
{{/each}}

---

## Recommended Action

{{RECOMMENDATION}}

**Next Steps**:
1. Address critical issues first
2. Fix high priority issues
3. Consider medium priority improvements
4. Re-run review after fixes

---

Reviewed by Claude Code PR Automation
Agents: code-reviewer, silent-failure-hunter, type-design-analyzer, pr-test-analyzer, comment-analyzer
```

## Review States

### APPROVE
Use when:
- No critical or high priority issues
- PR is ready to merge

**Title**: `✅ Review: Approved`

### REQUEST_CHANGES
Use when:
- Critical issues exist
- Must fix before merge

**Title**: `⚠️ Review: Request Changes`

### COMMENT
Use when:
- High priority issues but not blocking
- Suggestions for improvement
- PR is generally good but has issues

**Title**: `💬 Review: Comments`

## Variable Reference

| Variable | Description | Example |
|----------|-------------|---------|
| `REVIEW_STATE` | APPROVE, REQUEST_CHANGES, or COMMENT | REQUEST_CHANGES |
| `PR_NUMBER` | Pull request number | 28 |
| `PR_TITLE` | Pull request title | Add Back to Top button |
| `AUTHOR` | PR author username | fanioz |
| `BRANCH_NAME` | Branch name | feature/back-to-top |
| `ADDITIONS` | Lines added | 62 |
| `DELETIONS` | Lines deleted | 3 |
| `OVERALL_ASSESSMENT` | Summary of review | This PR has solid foundations but requires critical fixes... |
| `CRITICAL_COUNT` | Number of critical issues | 2 |
| `HIGH_COUNT` | Number of high issues | 3 |
| `MEDIUM_COUNT` | Number of medium issues | 1 |
| `RECOMMENDATION` | approve/request changes/comment | Request changes before merge |

## Issue Object Structure

### Critical Issue
```json
{
  "title": "SSR window object guard missing",
  "agent_name": "silent-failure-hunter",
  "file_path": "components/back-to-top.tsx",
  "line_number": 11,
  "confidence": 95,
  "impact": "Component will crash during SSR",
  "description": "The component accesses window directly without checking if it exists...",
  "fix_code": "if (typeof window === 'undefined') return;\n..."
}
```

### High Priority Issue
```json
{
  "title": "No browser compatibility fallback",
  "agent_name": "silent-failure-hunter",
  "file_path": "components/back-to-top.tsx",
  "line_number": 12,
  "confidence": 80,
  "description": "Smooth scroll behavior not supported in all browsers...",
  "fix_suggestion": "Add try-catch with instant scroll fallback"
}
```

### Medium Priority Issue
```json
{
  "agent_name": "code-reviewer",
  "description": "Consider using framer-motion for animations",
  "file_path": "components/back-to-top.tsx",
  "line_number": 47
}
```

### Positive Observation
```json
{
  "description": "Excellent accessibility with proper ARIA attributes"
}
```

## Example Output

```markdown
## ⚠️ Review: Request Changes

# PR #28: Add Back to Top button

**Author**: fanioz
**Branch**: palette-back-to-top-9963337949827599451
**Changes**: 62+ 3-

---

## Summary

This PR introduces a Back to Top button component with solid accessibility features and Neo Brutalism design compliance. However, it has critical issues that must be addressed before merge.

---

## 🚨 Critical Issues (Must Fix) - 2

### 1. SSR window object guard missing
- **Agent**: silent-failure-hunter
- **File**: `components/back-to-top.tsx:11`
- **Confidence**: 95%
- **Impact**: Component will crash during SSR, causing build failure

**Issue**:
The component accesses `window` directly without checking if it exists. In Next.js static export, this will cause a `ReferenceError` during server-side rendering.

**Fix**:
```typescript
const scrollToTop = () => {
  // Guard against SSR
  if (typeof window === "undefined") return

  try {
    window.scrollTo({
      top: 0,
      behavior: "smooth",
    })
  } catch (error) {
    console.error("Smooth scroll failed, using instant scroll:", error)
    window.scrollTo(0, 0)
  }
}
```

### 2. Missing error handling in scroll event handler
- **Agent**: silent-failure-hunter
- **File**: `components/back-to-top.tsx:22`
- **Confidence**: 90%
- **Impact**: State updates can fail silently in iframes/restricted contexts

**Issue**:
The `toggleVisibility` function accesses `window.scrollY` without error handling.

**Fix**:
```typescript
const toggleVisibility = () => {
  try {
    setIsVisible(window.scrollY > SCROLL_THRESHOLD)
  } catch (error) {
    console.error("Error accessing scroll position:", error)
  }
}
```

---

## ⚠️ High Priority Issues (Should Fix) - 3

### 1. No browser compatibility fallback
- **Agent**: silent-failure-hunter
- **File**: `components/back-to-top.tsx:12`
- **Confidence**: 80%

**Issue**:
The `behavior: "smooth"` option is not supported in Safari < 15.4.

**Suggested Fix**:
Add try-catch with instant scroll fallback (shown above)

---

## ✅ Positive Observations

- Excellent accessibility: Proper ARIA attributes, keyboard navigation
- Proper cleanup: Event listener correctly removed
- Neo Brutalism compliance: Bold borders, hard shadows, no rounded corners
- Good z-index: z-[100] won't conflict with Header (z-50)

---

## Recommended Action

Request changes before merge. Critical issues must be addressed first.

**Next Steps**:
1. Address critical issues first
2. Fix high priority issues
3. Consider medium priority improvements
4. Re-run review after fixes

---

Reviewed by Claude Code PR Automation
Agents: code-reviewer, silent-failure-hunter, type-design-analyzer, pr-test-analyzer, comment-analyzer
```
