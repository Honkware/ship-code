---
title: Names That Reveal Intent
impact: MEDIUM
impactDescription: Self-documenting code
tags: naming, readability, clean-code
---

## Names That Reveal Intent

`data`, `result`, `handler` — you read ten more lines just to find out what any of them actually do.

❌ (generic, needs context):

```
data = fetch()
result = process(data)
handler = Handler()
valid = check(x)
```

✓ (self-describing):

```
pending_orders = order_repo.fetch_pending()
invoice_totals = pending_orders.map(calculate_grand_total)
payment_gateway = PaymentGateway(api_key)
tax_id_is_valid = tax_authority.validate_format(tax_id)
```

Developers spend 60% of code review time decoding vague names. Good ones are free documentation.
