---
title: Domain-Based Directory Structure
impact: MEDIUM
impactDescription: Navigable codebase
tags: structure, organization, domains
---

## Domain-Based Directory Structure

`controllers/`, `services/`, `models/` — you see the frameworks, not the system.

❌ (technical layers obscure intent):

```
src/
    controllers/
    services/
    models/
    utils/
    helpers/
```

✓ (business domains reveal intent):

```
src/
    order/
        model/
        pricing/
        tax/
    payment/
        gateway/
        receipt/
    user/
        auth/
        profile/
```

New developers understand domain-organized codebases in half the time — the tree tells them what the system does, not which frameworks it uses.
