# Contributing to Claude Code PR Automation

Thank you for your interest in contributing to Claude Code PR Automation! This document provides guidelines and instructions for contributing.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How to Contribute](#how-to-contribute)
- [Development Setup](#development-setup)
- [Pull Request Process](#pull-request-process)
- [Coding Standards](#coding-standards)
- [Testing](#testing)
- [Documentation](#documentation)

---

## Code of Conduct

This project adheres to a code of conduct that all contributors are expected to follow:

- Be respectful and inclusive
- Provide constructive feedback
- Focus on what is best for the community
- Show empathy towards other community members

---

## How to Contribute

### Reporting Bugs

Before creating bug reports, please check existing issues to avoid duplicates.

When creating a bug report, include:

- **Clear title** describing the bug
- **Description** of what happened
- **Steps to reproduce** the issue
- **Expected behavior**
- **Actual behavior**
- **Environment** (OS, Claude Code version, gh CLI version)
- **Screenshots** if applicable

### Suggesting Enhancements

Enhancement suggestions are welcome! Please provide:

- **Clear title** describing the enhancement
- **Detailed description** of the proposed feature
- **Use cases** and benefits
- **Examples** of how it would work
- **Potential alternatives** considered

### Submitting Pull Requests

PRs are welcome! Please follow these guidelines:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Follow coding standards (see below)
5. Test your changes thoroughly
6. Commit with clear messages
7. Push to your fork (`git push origin feature/amazing-feature`)
8. Create a Pull Request

---

## Development Setup

### Prerequisites

- [Claude Code](https://claude.ai/code) installed
- [GitHub CLI](https://cli.github.com/) installed
- Git installed
- A code editor (VS Code, etc.)

### Clone and Setup

```bash
# Fork the repository on GitHub first

# Clone your fork
git clone https://github.com/YOUR_USERNAME/claude-code-pr-automation.git
cd claude-code-pr-automation

# Add upstream remote
git remote add upstream https://github.com/fanioz/claude-code-pr-automation.git

# Install the skill locally for testing
cp -r skills/pr-automation ~/.claude/skills/

# Verify installation
ls ~/.claude/skills/pr-automation/
```

### Making Changes

```bash
# Create a new branch
git checkout -b feature/your-feature-name

# Edit skill files
vim skills/pr-automation/SKILL.md

# Test your changes locally
# (Use Claude Code to test the skill)

# Commit your changes
git add skills/pr-automation/SKILL.md
git commit -m "feat: add new feature"

# Push to your fork
git push origin feature/your-feature-name
```

---

## Pull Request Process

### Before Submitting

1. **Test thoroughly** - Ensure your changes work as expected
2. **Update documentation** - Update README.md, reference.md, etc.
3. **Follow standards** - Adhere to coding standards (see below)
4. **Clean commits** - Use clear commit messages
5. **Sync with upstream** - Ensure your branch is up to date

```bash
# Sync with upstream
git fetch upstream
git rebase upstream/main
```

### PR Description

Your PR should include:

- **Clear title** describing the change
- **Detailed description** of what you changed and why
- **Related issues** (e.g., "Fixes #123")
- **Screenshots** if applicable
- **Testing notes** - How you tested the changes
- **Breaking changes** - If any, explain clearly

### PR Checklist

- [ ] Code follows project standards
- [ ] Documentation updated
- [ ] Tests pass (if applicable)
- [ ] Commit messages are clear
- [ ] PR description is complete
- [ ] No merge conflicts with upstream/main

---

## Coding Standards

### SKILL.md Guidelines

- Use clear, concise language
- Organize with clear headings
- Include examples for complex workflows
- Document all tool permissions
- Provide troubleshooting guidance

**Example**:
```markdown
## Step 1: Do This Thing

First, explain what we're doing and why.

```bash
# Show the command
gh pr view $NUMBER
```

Then explain what the output means.
```

### Reference.md Guidelines

- Document each agent thoroughly
- Include examples
- Specify when to use each agent
- Note confidence levels
- Provide output format details

### File Naming

- Use lowercase with hyphens: `review-comment.md`
- Be descriptive: `agent-documentation.md`
- Keep names under 30 characters

### Markdown Style

- Use ATX-style headings (`#`, `##`, `###`)
- Include blank lines before headings
- Use code blocks with language specifiers
- Link to related resources
- Use bullet points for lists

---

## Testing

### Manual Testing

1. **Install locally** - Copy skill to `~/.claude/skills/`
2. **Test triggers** - Try different trigger phrases
3. **Test workflows** - Run full review workflow
4. **Test edge cases** - Test error conditions
5. **Document issues** - Report any bugs found

### Test Scenarios

```bash
# Test 1: Basic review
"Review PR 28"

# Test 2: Review with fixes
"Review this PR and fix any issues"

# Test 3: Iteration
"Review PR 123 until it's ready"

# Test 4: Edge cases
# - PR doesn't exist
# - No gh authentication
# - Merge conflicts
# - Empty PR
```

### Test Checklist

- [ ] Skill triggers correctly
- [ ] All agents launch
- [ ] Reviews post to GitHub
- [ ] Fixes apply correctly
- [ ] Iteration works as expected
- [ ] Error handling works
- [ ] Documentation is accurate

---

## Documentation

### When to Update Documentation

Update documentation when you:

- Add new features
- Change workflows
- Fix bugs
- Add new agents
- Change configuration options

### What to Update

- **README.md** - If user-facing changes
- **SKILL.md** - If workflow changes
- **reference.md** - If agent behavior changes
- **CONTRIBUTING.md** - If contribution process changes
- **Comments** - If code needs clarification

### Documentation Style

- Use clear, simple language
- Provide examples
- Include screenshots where helpful
- Link to related docs
- Keep it up-to-date

---

## Commit Messages

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>: <description>

[optional body]

[optional footer]
```

**Types**:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Formatting changes
- `refactor`: Code refactoring
- `test`: Adding/updating tests
- `chore`: Build/process changes

**Examples**:
```
feat: add support for GitLab merge requests

The skill now supports GitLab MRs in addition to GitHub PRs.
Users can specify the platform with the --platform flag.

Closes #123
```

```
fix: handle missing gh CLI gracefully

Previously, the skill would crash if gh CLI was not installed.
Now it provides helpful error messages and installation instructions.

Fixes #456
```

---

## Getting Help

### Questions?

- Check [Documentation](README.md)
- Search [Issues](https://github.com/fanioz/claude-code-pr-automation/issues)
- Start a [Discussion](https://github.com/fanioz/claude-code-pr-automation/discussions)

### Need Support?

- Create an issue with the "question" label
- Describe what you're trying to do
- Include error messages or screenshots
- Share your environment details

---

## Recognition

Contributors will be recognized in:

- CONTRIBUTORS.md file
- Release notes
- Project README

---

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

## Additional Resources

- [Claude Code Documentation](https://claude.ai/code)
- [GitHub CLI Documentation](https://cli.github.com/manual/)
- [Git Documentation](https://git-scm.com/doc)

---

**Thank you for contributing! 🎉**

Every contribution helps make this project better for everyone.
