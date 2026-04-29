# Ship Code

Code quality guidelines for shipping production-grade software. Twenty rules across seven categories — from critical error-handling patterns down to project organization. Each rule includes incorrect/correct code examples.

Derived from analysis of 470+ real-world PRs, 304K AI-authored commits, and industry security research.

## Install

Via [skills.sh](https://skills.sh):

```bash
npx skills add owner/ship-code
```

Via [agentskill.sh](https://agentskill.sh):

```
/learn @owner/ship-code
```

## What's Covered

| Priority | Category | Rules |
|----------|----------|-------|
| CRITICAL | Error Handling & Resilience | fail-fast, no-empty-catch, calibrated-defense |
| CRITICAL | Security & Dependencies | env-for-secrets, validate-inputs, verify-deps |
| CRITICAL | Testing & Verification | behavior-not-coverage, edge-cases |
| HIGH | Architecture & Abstraction | match-complexity, boundaries, no-duplication, refactor-as-you-go |
| HIGH | Documentation & Communication | why-not-what, de-ai, readme-tested |
| MEDIUM | Naming & Readability | reveals-intent, consistent-vocabulary |
| MEDIUM | Code Structure | single-purpose, prefer-immutable, domain-structure |

## Repository Structure

```
ship-code/
  SKILL.md          # Skill index (lightweight)
  AGENTS.md         # Full compiled guide (all rules expanded)
  metadata.json     # Skill metadata
  rules/            # Individual rule files
    error-fail-fast.md
    error-no-empty-catch.md
    error-calibrated-defense.md
    sec-env-for-secrets.md
    sec-validate-inputs.md
    sec-verify-deps.md
    test-behavior-not-coverage.md
    test-edge-cases.md
    arch-match-complexity.md
    arch-boundaries.md
    arch-no-duplication.md
    arch-refactor-as-you-go.md
    doc-why-not-what.md
    doc-de-ai.md
    doc-readme-tested.md
    name-reveals-intent.md
    name-consistent-vocabulary.md
    struct-single-purpose.md
    struct-prefer-immutable.md
    struct-domain-structure.md
```

## Data Sources

- CodeRabbit analysis of 470 real-world PRs
- GitClear analysis of 304K AI-authored commits
- Veracode 2025 GenAI Code Security Report (100+ LLMs tested)
- GitGuardian 2025 Secrets Exposure Report
- USENIX Security 2025 (slopsquatting study)
- Liu et al. 2025 academic study on LLM code robustness
- EvalPlus framework boundary testing results

## License

MIT
