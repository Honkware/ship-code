---
title: Validate All External Input
impact: CRITICAL
impactDescription: Prevents injection attacks
tags: security, input-validation, sanitization
---

## Validate All External Input

Veracode 2025 tested 100+ LLMs: 86% failed XSS prevention, 88% failed log injection. The models skipped validation entirely.

❌

```
function get_user(id):
    return db.raw_query("SELECT * FROM users WHERE id = {id}")
```

✓

```
function get_user(id: UUID) -> User:
    assert id.is_valid(), "Invalid user ID"
    return users.find_by_id(id)
```

HTTP params, files, env vars, external APIs — every entry point is an injection surface.
