---
name: ship-code
description: >
  Code quality guidelines for writing, reviewing, and refactoring production-grade software.
  Use when the user asks to write code, review code, fix bugs, improve quality,
  refactor, add tests, write documentation or READMEs, handle errors, validate inputs,
  manage secrets, choose dependencies, name variables/functions, or structure a project.
  Also use when the user mentions AI-generated code, vibe coding, code review,
  best practices, clean code, production readiness, or wants output that reads
  as human-written rather than AI-generated.
  Covers error handling, security, testing, architecture, documentation, naming,
  and code structure with 20 prioritized rules and incorrect/correct examples.
license: MIT
metadata:
  author: ship-code
  version: "1.0.0"
---

# Ship Code

Code quality guidelines for shipping production-grade software. Twenty rules across seven categories, from critical error-handling patterns down to project organization. Each rule includes incorrect/correct code examples.

## When to Apply

Reference these guidelines when:
- Writing or editing code, tests, documentation, or configuration
- Reviewing AI-generated output before committing
- Starting new projects or adding features
- Refactoring existing code
- Any time output needs to survive a senior engineer's code review

## Rule Categories by Priority

| Priority | Category | Prefix |
|----------|----------|--------|
| 1 | Error Handling & Resilience | `error-` |
| 2 | Security & Dependencies | `sec-` |
| 3 | Testing & Verification | `test-` |
| 4 | Architecture & Abstraction | `arch-` |
| 5 | Documentation & Communication | `doc-` |
| 6 | Naming & Readability | `name-` |
| 7 | Code Structure | `struct-` |

## Quick Reference

### 1. Error Handling & Resilience (CRITICAL)

- `error-fail-fast` - Return explicit errors. Never swallow exceptions into silent defaults
- `error-no-empty-catch` - Catch only what you can handle. Re-throw everything else
- `error-calibrated-defense` - Handle expectable errors gracefully. Crash on preventable ones

### 2. Security & Dependencies (CRITICAL)

- `sec-env-for-secrets` - No secrets in source. Env vars only, validated at startup
- `sec-validate-inputs` - Treat external input as hostile until sanitized
- `sec-verify-deps` - Verify before installing. Prefer standard library. Pin versions

### 3. Testing & Verification (CRITICAL)

- `test-behavior-not-coverage` - Verify business behavior with spec-derived expected values
- `test-edge-cases` - Test boundaries: empty, zero, null, max, wrong types

### 4. Architecture & Abstraction (HIGH)

- `arch-match-complexity` - Match solution complexity to problem complexity
- `arch-boundaries` - Business logic separate from I/O. Thin HTTP adapters
- `arch-no-duplication` - One canonical implementation per concept. Never copy-paste
- `arch-refactor-as-you-go` - Consolidate every 2-3 features. Extract, rename, deduplicate

### 5. Documentation & Communication (HIGH)

- `doc-why-not-what` - Comments explain decisions and trade-offs, not code mechanics
- `doc-de-ai` - Replace mood words with concrete specifics. No filler
- `doc-readme-tested` - READMEs need tested steps, troubleshooting, known issues

### 6. Naming & Readability (MEDIUM)

- `name-reveals-intent` - Names answer why something exists and what it does
- `name-consistent-vocabulary` - One name per concept across the codebase

### 7. Code Structure (MEDIUM)

- `struct-single-purpose` - One function, one job. Extract until each has a clear name
- `struct-prefer-immutable` - Return new state. Avoid shared mutable state
- `struct-domain-structure` - Organize by business domain, not technical layer

## How to Use

Read individual rule files for detailed explanations and code examples:

```
rules/error-fail-fast.md
rules/sec-env-for-secrets.md
rules/arch-match-complexity.md
```

Each rule file contains:
- Brief explanation of why it matters
- Incorrect code example with explanation
- Correct code example with explanation
- Context and references

## Full Compiled Document

For the complete guide with all rules expanded: `AGENTS.md`
