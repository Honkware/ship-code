---
name: ship-code
description: >
  Reviews code, tests, documentation, and configuration against 20 production-grade
  quality rules. Checks error handling, security boundaries, test validity, architecture,
  documentation clarity, naming conventions, and code structure. Use when writing code,
  reviewing code, fixing bugs, refactoring, adding features, writing tests or docs,
  handling secrets, validating inputs, choosing dependencies, naming things, or structuring
  projects. Also use when output needs to survive code review, when the user mentions
  AI-generated code, vibe coding, clean code, production readiness, or maintainability.
pattern: reviewer
tags:
  - code-quality
  - error-handling
  - security
  - testing
  - architecture
  - clean-code
  - best-practices
  - documentation
license: MIT
metadata:
  author: ship-code
  version: "1.0.0"
---

# ship-code

Code quality reviewer. 20 rules across 7 categories. Checks code before it ships.

## When to Apply

Apply this skill when:
- Writing or editing code, tests, documentation, or configuration
- Reviewing code (your own or someone else's, including AI-generated)
- Fixing bugs or refactoring existing code
- Adding features to a codebase
- Writing tests, READMEs, API docs, or inline comments
- Handling secrets, credentials, or environment configuration
- Validating external inputs
- Choosing or installing dependencies
- Naming variables, functions, classes, or files
- Structuring or reorganizing a project

Also applies when the user mentions:
- Code review, quality, or standards
- AI-generated code, vibe coding, or Copilot output
- Clean code, production readiness, or maintainability
- Security review or input validation

## Instructions

1. Read the relevant rule files based on the task:
   - Error handling issues → `rules/error-*.md`
   - Security or dependency issues → `rules/sec-*.md`
   - Testing issues → `rules/test-*.md`
   - Architecture or structure issues → `rules/arch-*.md` or `rules/struct-*.md`
   - Documentation issues → `rules/doc-*.md`
   - Naming issues → `rules/name-*.md`

2. For each rule, compare the user's code against the ❌ and ✓ examples.

3. Flag violations with:
   - The rule prefix (e.g., `error-fail-fast`)
   - A one-line explanation of the problem
   - A concrete fix following the ✓ example pattern

4. Prioritize by severity:
   - CRITICAL (error-, sec-, test-): Must fix before shipping
   - HIGH (arch-, doc-): Should fix before merging
   - MEDIUM (name-, struct-): Fix when touching nearby code

5. After reviewing, suggest 1-3 specific refactors with the biggest impact.

## Rules

### CRITICAL: Error Handling & Resilience

| Rule | Checks |
|------|--------|
| `error-fail-fast` | Explicit errors returned, not swallowed into defaults |
| `error-no-empty-catch` | Every catch block does real work, no log-and-continue |
| `error-calibrated-defense` | Expectable errors handled, preventable errors crash |

### CRITICAL: Security & Dependencies

| Rule | Checks |
|------|--------|
| `sec-env-for-secrets` | Secrets in env vars, validated at startup |
| `sec-validate-inputs` | Every external input sanitized at boundaries |
| `sec-verify-deps` | Dependencies verified, stdlib preferred, versions pinned |

### CRITICAL: Testing & Verification

| Rule | Checks |
|------|--------|
| `test-behavior-not-coverage` | Tests verify business logic, not just execute code |
| `test-edge-cases` | Boundaries tested: empty, null, zero, max, wrong types |

### HIGH: Architecture & Abstraction

| Rule | Checks |
|------|--------|
| `arch-match-complexity` | Solution complexity matches problem complexity |
| `arch-boundaries` | Business logic separate from I/O, thin HTTP adapters |
| `arch-no-duplication` | One canonical implementation, no copy-paste |
| `arch-refactor-as-you-go` | Complexity consolidated every 2-3 features |

### HIGH: Documentation & Communication

| Rule | Checks |
|------|--------|
| `doc-why-not-what` | Comments explain decisions, not code mechanics |
| `doc-de-ai` | Concrete specifics, no mood words or filler |
| `doc-readme-tested` | READMEs with tested steps, troubleshooting, known issues |

### MEDIUM: Naming & Readability

| Rule | Checks |
|------|--------|
| `name-reveals-intent` | Names answer why something exists |
| `name-consistent-vocabulary` | One name per concept across codebase |

### MEDIUM: Code Structure

| Rule | Checks |
|------|--------|
| `struct-single-purpose` | One function, one job |
| `struct-prefer-immutable` | New state returned, not shared mutation |
| `struct-domain-structure` | Folders organized by business domain |

## Output Format

Report findings as:

```
[SEVERITY] rule-prefix: one-line problem description
  Fix: concrete correction
```

Example:
```
[CRITICAL] error-no-empty-catch: catch block logs but continues execution
  Fix: handle PaymentDeclined specifically, re-throw unexpected errors
[HIGH] arch-no-duplication: input validation copied in 3 endpoints
  Fix: extract into require_fields() utility, use everywhere
[MEDIUM] name-reveals-intent: variable 'data' doesn't indicate content type
  Fix: pending_orders = order_repo.fetch_pending()
```

## Examples

### Example 1: Reviewing a route handler

User submits:
```javascript
app.get("/report", async (req, res) => {
  const user = await db.query("SELECT * FROM users WHERE id = ?", [req.session.userId]);
  if (!user.isAdmin && req.query.scope === "all") return res.status(403).json({error: "no"});
  const data = await db.query("SELECT * FROM reports WHERE scope = ?", [req.query.scope]);
  res.json(data);
});
```

Review output:
```
[CRITICAL] sec-validate-inputs: req.query.scope interpolated directly into SQL
  Fix: use parameterized queries or an ORM. Validate scope against allowed values.
[HIGH] arch-boundaries: business logic (admin check, query building) mixed with HTTP handler
  Fix: move admin check and report generation to a service function. Keep route thin.
[MEDIUM] name-reveals-intent: 'data' is generic
  Fix: reports = await report_service.generate(scope, current_user)
```

### Example 2: Reviewing a README

User submits a README with: "A comprehensive, robust solution for modern web development."

Review output:
```
[HIGH] doc-de-ai: "comprehensive" and "robust" are AI tells without specifics
  Fix: "Payment webhook processor. Handles 5k Stripe events/min at peak."
[HIGH] doc-readme-tested: no troubleshooting section, no tested setup steps
  Fix: add Prerequisites, install commands, and a troubleshooting table
```

## Rule Files

Full rules with code examples:
```
rules/error-fail-fast.md
rules/error-no-empty-catch.md
rules/error-calibrated-defense.md
rules/sec-env-for-secrets.md
rules/sec-validate-inputs.md
rules/sec-verify-deps.md
rules/test-behavior-not-coverage.md
rules/test-edge-cases.md
rules/arch-match-complexity.md
rules/arch-boundaries.md
rules/arch-no-duplication.md
rules/arch-refactor-as-you-go.md
rules/doc-why-not-what.md
rules/doc-de-ai.md
rules/doc-readme-tested.md
rules/name-reveals-intent.md
rules/name-consistent-vocabulary.md
rules/struct-single-purpose.md
rules/struct-prefer-immutable.md
rules/struct-domain-structure.md
```

## Full Compiled Document

All rules expanded with examples: `AGENTS.md`
