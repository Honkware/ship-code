---
title: Separate Business Logic from I/O
impact: HIGH
impactDescription: Cleaner architecture
tags: architecture, separation-of-concerns, layering
---

## Separate Business Logic from I/O

Business logic stays pure; handlers stay thin.

**❌ (handler does everything):**

```
route "/report":
    user = db.raw_query("SELECT ...", [session.user_id])
    if not user.is_admin and query.scope == "all":
        return 403
    data = db.raw_query("... complex SQL ...")
    return json(format_for_frontend(data))
```

**✓ (thin adapter, pure business logic):**

```
function generate_report(scope, user) -> Report:
    assert can_view(user, scope), "Unauthorized"
    return report_repo.generate(scope)

route "/report":
    return json(generate_report(query.scope, current_user))
```

80% architectural violation rate in LLM-generated code — "Hallucinated Coupling."
