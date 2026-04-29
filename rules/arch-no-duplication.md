---
title: No Code Duplication
impact: HIGH
impactDescription: Fewer duplicate bugs
tags: architecture, dry, refactoring
---

## No Code Duplication

Copy-paste is a bug multicast protocol — one mistake, many destinations.

**❌ (same validation copied everywhere):**

```
create_order(body):
    if body is null or body.items is null:
        return 400, "Items required"

create_invoice(body):
    if body is null or body.items is null:
        return 400, "Items required"
```

**✓ (shared utility, single source of truth):**

```
function require_fields(body, fields):
    missing = fields.filter(f => body[f] is null)
    assert missing.is_empty(), "Missing: {missing}"

create_order(body):
    require_fields(body, ["items"])

create_invoice(body):
    require_fields(body, ["items"])
```

GitClear: copy-pasted code rose from 8.3% to 12.3% since AI adoption; refactoring dropped from 25% to <10%.
