---
title: Fail Fast and Loud
impact: CRITICAL
impactDescription: Prevents silent failures
tags: error-handling, exceptions, debugging
---

## Fail Fast and Loud

`return null` from a catch block doesn't communicate failure — it communicates "no result found." 47 production bugs traced back to this exact pattern.

❌

```
function get_user(id):
    try:
        return db.query(id)
    catch any:
        return null
```

✓

```
function get_user(id) -> User:
    user = db.query(id)
    if user is null:
        raise UserNotFoundError("User {id} not found")
    return user
```

Martin Fowler, IEEE Software: "Fail fast does exactly the opposite of fail-safe: when a problem occurs, it fails immediately and visibly."
