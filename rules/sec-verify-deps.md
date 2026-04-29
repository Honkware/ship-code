---
title: Verify Dependencies Before Installing
impact: CRITICAL
impactDescription: Prevents supply-chain attacks
tags: security, dependencies, supply-chain
---

## Verify Dependencies Before Installing

Check it exists, check the date, check for CVEs. When in doubt: stdlib.

**❌ (installing a hallucinated package name):**

```
// Hallucinated package — does not exist
npm install express-mongoose-auth-utils
```

**✓ (verification checklist):**

```
// 1. Verify on official registry
// 2. Check download count, last update, known CVEs
// 3. Review for postinstall scripts
// 4. Pin exact version
// 5. Run security audit after adding
// When in doubt: use the standard library
```

19.7% of AI-recommended packages are hallucinated (USENIX Security 2025); attackers register those names and inject malware — "slopsquatting."
