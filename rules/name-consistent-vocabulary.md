---
title: Consistent Vocabulary
impact: MEDIUM
impactDescription: Predictable codebase
tags: naming, consistency, conventions
---

## Consistent Vocabulary

`get_user` and `fetch_customer` — same function, two names, twice the cognitive load.

❌ (same concept, three names):

```
function get_user(id)
function fetch_customer(id)
function retrieve_client(id)
```

✓ (consistent pattern, learned once):

```
function get_user(id)
function get_user_by_email(email)
function get_user_preferences(id)
```

Boring consistency beats creative variety every time. Learn the pattern once, read fluently everywhere.
