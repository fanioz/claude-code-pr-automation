# Claude Code PR Automation

<div align="center">

**Automate your entire pull request review workflow with AI-powered agents**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude_Code-Skill-blue)](https://claude.ai/code)
[![PR Review](https://img.shields.io/badge/PR_Review-Automated-green)](https://github.com/fanioz/claude-code-pr-automation)

[Features](#features) • [Installation](#installation) • [Usage](#usage) • [Examples](#examples) • [Contributing](#contributing)

</div>

---

## Overview

**Claude Code PR Automation** is a custom skill for Claude Code that automates the entire pull request review workflow. It uses specialized AI agents to comprehensively review your code, post reviews to GitHub, fix issues with your approval, and iterate until your PR is ready to merge.

### What It Does

- 🔍 **Comprehensive Code Reviews** - Uses 5 specialized AI agents in parallel
- 📝 **Automated Review Comments** - Posts detailed feedback to GitHub PRs
- 🔧 **Auto-Fix with Approval** - Fixes issues automatically after you approve
- 🔄 **Iterative Improvement** - Re-reviews until PR is mergeable
- ✅ **Final Approval** - Approves PR when all critical issues are resolved

---

## Features

### 🤖 5 Specialized Review Agents

| Agent | Purpose | Confidence |
|-------|---------|------------|
| **code-reviewer** | General code quality, security, performance | 90-100% |
| **silent-failure-hunter** | Error handling, edge cases, fallback behavior | 90-100% |
| **type-design-analyzer** | Type safety, encapsulation, invariants | 80-89% |
| **pr-test-analyzer** | Test coverage, edge cases, test quality | 80-89% |
| **comment-analyzer** | Documentation accuracy, completeness | 70-79% |

### 🎯 Smart Issue Prioritization

- **Critical (90-100%)** - Must fix before merge (security, crashes, breaking changes)
- **High (80-89%)** - Should fix (performance, best practices, type safety)
- **Medium (70-79%)** - Nice to have (code style, documentation)

### 🔄 Automated Workflow

1. Fetch PR from GitHub
2. Launch all review agents in parallel
3. Aggregate findings by priority
4. Post comprehensive review to GitHub
5. Ask for approval before fixing issues
6. Apply fixes and push to branch
7. Re-run review (max 5 iterations)
8. Approve PR when ready

---

## Installation

### Option 1: Install from Marketplace (Recommended)

```bash
# Coming soon to Claude Code Plugin Marketplace
```

### Option 2: Manual Installation

```bash
# Clone the repository
git clone https://github.com/fanioz/claude-code-pr-automation.git
cd claude-code-pr-automation

# Copy skill to your Claude skills directory
cp -r skills/pr-automation ~/.claude/skills/

# Verify installation
ls ~/.claude/skills/pr-automation/
# Should show: SKILL.md, reference.md, templates/
```

### Option 3: Install as Project Skill

```bash
# In your project directory
mkdir -p .claude/skills
cp -r skills/pr-automation .claude/skills/

# Commit to your repo
git add .claude/skills/pr-automation
git commit -m "Add PR automation skill"
```

### Prerequisites

- ✅ [Claude Code](https://claude.ai/code) CLI installed
- ✅ [GitHub CLI](https://cli.github.com/) (`gh`) installed and authenticated
- ✅ Git installed and configured

---

## Usage

### Basic Usage

```bash
# Start Claude Code
claude

# Trigger the skill with natural language
You: Review PR 28

You: Check PR 123 for issues

You: Review this PR and fix any problems

You: Iterate on PR 45 until it's ready to merge
```

### Skill Triggers

The skill automatically activates when you use phrases like:

- "Review PR [number]"
- "Check pull request for issues"
- "Fix PR problems"
- "Review this PR"
- "Check if there are any issues"
- "Review and fix"

### What Happens

1. **PR Detection** - Skill identifies which PR to review
2. **Agent Launch** - 5 specialized agents analyze your code in parallel
3. **Review Aggregation** - Findings organized by priority
4. **GitHub Review** - Comprehensive review posted to GitHub
5. **Fix Approval** - You're asked to approve fixes
6. **Auto-Fix** - Issues fixed and pushed to branch (if approved)
7. **Iteration** - Process repeats until PR is ready
8. **Approval** - PR approved when all critical issues resolved

---

## Examples

### Example 1: Simple Review

```
You: Review PR 28

Claude: I'll review PR 28 for you.

[Launches 5 agents in parallel...]

Found 2 critical issues and 3 high priority issues.

🚨 Critical Issues:
1. SSR window object guard missing (components/back-to-top.tsx:11)
2. No error handling in scrollToTop (components/back-to-top.tsx:12)

⚠️ High Priority Issues:
1. No browser compatibility fallback
2. Missing hydration mismatch prevention
3. Scroll event listener not throttled

✅ Positive Observations:
- Excellent accessibility features
- Proper cleanup patterns

Review posted to GitHub. Found 5 issues total.

Do you want to apply these fixes?
- Apply all fixes automatically
- Review each fix before applying
- I'll fix manually

You: Apply all fixes

[Fixes applied, committed, and pushed...]

Iteration 2/5:
- Critical issues: 0
- High priority issues: 0

✅ All issues resolved! Approving PR...
```

### Example 2: Full Automation

```
You: Review PR 123 and iterate until it's ready

Claude: I'll review PR 123 and keep iterating until it's mergeable.

[Runs full automated workflow...]

Iteration 1/5: Found 7 issues
[Fixes applied...]

Iteration 2/5: Found 3 issues
[Fixes applied...]

Iteration 3/5: Found 0 issues

✅ PR approved and ready to merge!
```

### Example 3: Review Only

```
You: Check if there are any issues with the current PR

Claude: I'll review the current PR for issues.

[Launches agents, posts review...]

Found 1 high priority issue.
No fixes will be applied unless you request them.

You: Please fix it

[Fix applied and pushed...]
```

---

## Configuration

### Customize Agent Selection

Edit `skills/pr-automation/SKILL.md` to customize which agents run:

```yaml
# Always run these agents
- code-reviewer

# Run for code files (.ts, .tsx, .js, etc.)
- silent-failure-hunter
- type-design-analyzer

# Run for test files
- pr-test-analyzer

# Run for documentation
- comment-analyzer
```

### Adjust Max Iterations

Change the maximum number of review iterations:

```markdown
## Workflow Steps
### 11. Iterate (Loop)
Continue until:
- No critical or high issues remain
- Max iterations reached (5)  # ← Change this number
```

### Customize Review Template

Edit `skills/pr-automation/templates/review-comment.md` to customize the review format.

---

## How It Works

### Agent Architecture

```
User Request
    ↓
PR Detection (gh pr view)
    ↓
Parallel Agent Launch
    ├─→ code-reviewer ────────┐
    ├─→ silent-failure-hunter─┤
    ├─→ type-design-analyzer──┼→→ Aggregation
    ├─→ pr-test-analyzer ──────┤
    └─→ comment-analyzer ──────┘
                              ↓
                        Priority Sorting
                              ↓
                        Review Generation
                              ↓
                        GitHub (gh pr review)
                              ↓
                        User Approval
                              ↓
                   [Apply Fixes] → [Push]
                              ↓
                         Re-review
                              ↓
                      [Loop until done]
```

### Tool Permissions

The skill uses these Claude Code tools:

- **Read, Grep, Glob** - Read code files
- **Bash** - Execute git and gh commands
- **Task** - Launch review agents
- **AskUserQuestion** - Get user approval
- **Edit, Write** - Apply fixes to code

---

## Requirements

- **Claude Code** - Latest version from [claude.ai/code](https://claude.ai/code)
- **GitHub CLI** - Install from [cli.github.com](https://cli.github.com/)
- **Git** - Install from [git-scm.com](https://git-scm.com/)

### Authentication

```bash
# Authenticate with GitHub
gh auth login

# Verify authentication
gh auth status
```

---

## Troubleshooting

### Skill Not Triggering

**Problem**: Skill doesn't activate when I ask to review a PR.

**Solutions**:
- Ensure skill is in `~/.claude/skills/pr-automation/`
- Check that `SKILL.md` exists and has valid YAML frontmatter
- Try using different trigger phrases: "Review PR", "Check pull request"

### Agents Not Launching

**Problem**: Review agents fail to launch.

**Solutions**:
- Verify `Task` tool is in `allowed-tools` in SKILL.md
- Check you have internet connection for agent API calls
- Try restarting Claude Code

### Reviews Not Posting

**Problem**: Reviews don't appear on GitHub.

**Solutions**:
- Run `gh auth status` to verify authentication
- Check you have write access to the repository
- Verify PR number is correct

### Fixes Not Applying

**Problem**: Fixes fail to apply or push.

**Solutions**:
- Ensure you're on the correct branch
- Check for merge conflicts: `git status`
- Verify file paths are correct
- Check you have push permissions

### Git Push Failing

**Problem**: `git push` fails after applying fixes.

**Solutions**:
```bash
# Check for merge conflicts
git status

# Resolve conflicts if needed
git mergetool

# Try pushing again
git push
```

---

## Development

### Project Structure

```
claude-code-pr-automation/
├── skills/
│   └── pr-automation/
│       ├── SKILL.md                # Main skill file
│       ├── reference.md            # Agent documentation
│       └── templates/
│           └── review-comment.md   # Review template
├── README.md                       # This file
├── LICENSE                         # MIT License
├── CONTRIBUTING.md                 # Contributing guidelines
└── .github/
    └── workflows/
        └── test.yml                # CI/CD (future)
```

### Adding New Agents

To add a new review agent:

1. Create agent in your codebase
2. Update `reference.md` with agent documentation
3. Add agent to workflow steps in `SKILL.md`
4. Test with sample PRs

### Customizing Workflows

Edit `skills/pr-automation/SKILL.md` to customize:

- Which agents to launch
- Issue prioritization thresholds
- Fix application strategies
- Iteration limits
- Error handling

---

## Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### Ways to Contribute

- 🐛 Report bugs
- 💡 Suggest new features
- 📖 Improve documentation
- 🔧 Fix issues
- ✨ Add new agents

---

## Roadmap

### v1.1 (Planned)
- [ ] Support for GitLab merge requests
- [ ] Support for Bitbucket pull requests
- [ ] Custom agent configurations per repository
- [ ] Integration with CI/CD pipelines

### v2.0 (Future)
- [ ] Web dashboard for review history
- [ ] Team configuration and sharing
- [ ] Custom rule engines
- [ ] Analytics and reporting

---

## FAQ

**Q: Does this work with private repositories?**
A: Yes! As long as you have access through `gh` CLI.

**Q: Can I use this with any programming language?**
A: Yes, agents are language-agnostic and work with any code.

**Q: How long does a review take?**
A: Typically 2-5 minutes for most PRs, depending on size.

**Q: What if I disagree with a review?**
A: You can choose to skip fixes or fix issues manually.

**Q: Can I run this on PRs I didn't create?**
A: Yes, as long as you have read access to the repository.

**Q: Does this modify my code automatically?**
A: No, fixes are only applied after you approve them.

---

## License

MIT License - see [LICENSE](LICENSE) for details.

---

## Acknowledgments

- Built with [Claude Code](https://claude.ai/code)
- Uses [GitHub CLI](https://cli.github.com/)
- Powered by [Claude Sonnet 4.5](https://anthropic.com)

---

## Support

- 📖 [Documentation](https://github.com/fanioz/claude-code-pr-automation/wiki)
- 🐛 [Issue Tracker](https://github.com/fanioz/claude-code-pr-automation/issues)
- 💬 [Discussions](https://github.com/fanioz/claude-code-pr-automation/discussions)

---

<div align="center">

**⭐ Star this repo if you find it useful!**

Made with ❤️ by [fanioz](https://github.com/fanioz)

[← Back to Top](#claude-code-pr-automation)

</div>
