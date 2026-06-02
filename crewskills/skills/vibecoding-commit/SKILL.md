---
name: vibecoding-commit
description: CrewAgent VibeCoding Commit — generates Conventional Commits, structured PR descriptions, and CHANGELOG entries from git diffs and CrewAI task outputs. Use to create professional, standardized commits after code passes review.
---

# VibeCoding Commit

Transform code changes and CrewAI task outputs into professional, standardized git commits and pull request descriptions.

## Role

You are a Git Workflow Specialist and Release Manager in the VibeCoding methodology.

## Process

### Phase 1: Analyze Changes
- Run `git diff --stat` to understand scope
- Run `git diff` to review actual changes
- Parse CrewAI task outputs for context on what was implemented
- Identify logical commit boundaries (one commit per concern)

### Phase 2: Generate Conventional Commits
Follow the Conventional Commits specification:

```
<type>(<scope>): <description>

<body>

<footer>
```

**Types**: feat, fix, refactor, perf, test, docs, ci, chore, style
**Scope**: The module/component affected (from project structure)
**Description**: Imperative mood, ≤ 72 chars, no period at end
**Body**: What changed and why (not how), wrapped at 72 chars
**Footer**: Breaking changes (BREAKING CHANGE:), issues closed (Closes #123), co-authors

### Phase 3: Generate PR Description

```markdown
## Summary
[One paragraph describing the overall change]

## Changes
- **feat(scope): description** — What and why
- **test(scope): description** — What was tested

## CrewAI Task Context
- Task: [task_name from tasks.yaml]
- Agent: [agent_name from agents.yaml]
- Expected Output: [summary of expected_output]

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manual testing steps completed

## Screenshots / Evidence
[If applicable]

## Checklist
- [ ] Code reviewed (vibecoding-review passed)
- [ ] All guardrails passing
- [ ] No hardcoded secrets
- [ ] Documentation updated

🤖 Generated with [Claude Code](https://claude.com/claude-code)
```

### Phase 4: Generate Changelog Entry

```markdown
## [version] — YYYY-MM-DD

### Added
- feat(scope): description

### Changed
- refactor(scope): description

### Fixed
- fix(scope): description

### Deprecated
- Items being phased out

### Security
- Security-related changes
```

## Guidelines
- One logical change per commit — never bundle unrelated changes
- Commit messages are for humans first, machines second
- Reference CrewAI task IDs and agent names for traceability
- Always include the Claude Code co-author trailer
- Verify no secrets, .env files, or build artifacts are staged
- Run `git status` to confirm staging before suggesting commit command

## Examples

**Input:** Staged changes: WebSocket manager + tests
**Output:** Commit: feat(websocket): add ConnectionManager with room-based pub/sub + heartbeat. PR body with architecture diagram, task context from crewai, test coverage report, checklist

**Input:** Staged changes: embedding pipeline + cache layer
**Output:** Commit: feat(search): implement batch embedding pipeline with Redis cache. PR body with performance benchmarks, CrewAI task trace, integration test results

**Input:** Staged changes: notification dispatcher + 3 channel implementations
**Output:** Commits: feat(notify): add NotificationDispatcher base + feat(notify): add email/Slack/in-app channels. PR body with channel abstraction design, rate limiting strategy, fallback logic docs
