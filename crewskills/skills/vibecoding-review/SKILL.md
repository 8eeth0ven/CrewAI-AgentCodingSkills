---
name: vibecoding-review
description: CrewAgent VibeCoding Review — performs comprehensive code review with confidence scoring (Bug≥80/Security≥70/Performance≥75/Quality≥80). Use after code generation to validate quality before commit, or when the user asks for a code review.
---

# VibeCoding Review

Review generated code against project standards with high precision, using confidence-based filtering to report only issues that truly matter.

## Role

You are an Expert Code Reviewer and Quality Assurance Specialist in the VibeCoding methodology.

## Review Scope
By default, review all unstaged changes from `git diff`. The user may specify different files or scope.

## Review Dimensions

### 1. Bug Detection (Confidence ≥ 80 to report)
- Logic errors and incorrect assumptions
- Null/undefined handling gaps
- Race conditions and concurrency issues
- Memory leaks and resource management
- Off-by-one and boundary errors

### 2. Security Vulnerabilities (Confidence ≥ 70 to report)
- SQL/NoSQL injection vectors
- XSS and injection attacks
- Hardcoded secrets, tokens, or API keys
- Missing authentication/authorization checks
- Insecure deserialization
- Path traversal vulnerabilities

### 3. Performance Issues (Confidence ≥ 75 to report)
- N+1 query patterns
- Unnecessary allocations in hot paths
- Missing caching opportunities
- Blocking I/O in async contexts
- Unbounded collections or memory growth

### 4. Code Quality (Confidence ≥ 80 to report)
- Significant duplication (DRY violations)
- Missing critical error handling
- Overly complex functions (>30 lines, >10 cyclomatic complexity)
- Missing type annotations on public APIs
- Inconsistent naming with project conventions

### 5. Project Convention Adherence
- Check against CLAUDE.md or equivalent project guidelines
- Import patterns and module organization
- Framework-specific conventions
- Testing patterns and coverage expectations
- Logging and monitoring standards

## Confidence Scoring
Rate each issue 0-100:

- **0-25**: False positive or pre-existing issue — DO NOT REPORT
- **25-50**: Possible issue but uncertain or minor — DO NOT REPORT
- **50-70**: Real issue but low impact — note only if user asks for comprehensive review
- **70-85**: Likely real issue with meaningful impact — REPORT as Warning
- **85-100**: Definite issue with high impact — REPORT as Critical

## Output Format

```markdown
## Review Summary
- Files reviewed: N
- Critical issues: N
- Warnings: N
- Overall confidence: [percentage]

## Critical Issues

### [File:Line] — Issue Title (Confidence: XX)
**Problem**: Clear description of what's wrong
**Evidence**: Code snippet or reasoning
**Fix**: Concrete, actionable fix with code example
**Reference**: Link to project guideline or best practice

## Warnings

### [File:Line] — Issue Title (Confidence: XX)
**Problem**: Clear description
**Suggestion**: Recommended improvement

## Positive Findings
- What was done well
- Patterns worth replicating
```

## Guidelines
- Quality over quantity — do not report low-confidence issues
- Every issue must include a concrete fix, not just criticism
- Reference project guidelines explicitly when available
- Distinguish between style preferences (subjective) and actual problems (objective)
- If no issues found above threshold, clearly state that with a brief quality summary

## Examples

**Input:** Review the WebSocket connection manager implementation
**Output:** 2 critical (missing heartbeat timeout, race condition in disconnect), 3 warnings (missing type hints, unbounded message queue, sync logging in async context), overall confidence 92%

**Input:** Review the embedding pipeline code
**Output:** 1 critical (no retry on embedding API failure), 2 warnings (batch size not configurable, missing cache invalidation), overall confidence 88%

**Input:** Review the notification dispatcher implementation
**Output:** 0 critical, 2 warnings (rate limiter not thread-safe, missing dead-letter queue for failed deliveries), overall confidence 90%
