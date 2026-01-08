# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.0.0] - 2025-01-08

### Added
- Initial release of Claude Code PR Automation skill
- 5 specialized review agents (code-reviewer, silent-failure-hunter, type-design-analyzer, pr-test-analyzer, comment-analyzer)
- Automated PR review workflow with GitHub integration
- Parallel agent execution for faster reviews
- Smart issue prioritization (critical/high/medium)
- User approval workflow for applying fixes
- Automatic iteration until PR is mergeable
- Comprehensive documentation and reference materials
- MIT License

### Features
- Fetch PR from GitHub automatically
- Launch 5 specialized AI agents in parallel
- Aggregate findings by priority
- Post detailed reviews to GitHub
- Apply fixes with user approval
- Push changes to PR branch
- Re-review until PR is ready
- Final approval when all issues resolved

### Documentation
- Comprehensive README with usage examples
- Agent reference documentation
- Review comment templates
- Contributing guidelines
- MIT License

[Unreleased]: https://github.com/fanioz/claude-code-pr-automation/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/fanioz/claude-code-pr-automation/releases/tag/v1.0.0
