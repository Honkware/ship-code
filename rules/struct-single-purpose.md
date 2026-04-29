---
title: Single Purpose Functions
impact: MEDIUM
impactDescription: Composable code
tags: structure, functions, composition
---

## Single Purpose Functions

Extract until the parent function reads like a summary.

❌ (80-line function, 6 concerns):

```
function handle_order(order):
    // validate (15 lines)
    // price with discounts (20 lines)
    // calculate tax (15 lines)
    // process payment (10 lines)
    // send email (10 lines)
    // audit log (10 lines)
```

✓ (composed from named steps):

```
function process_order(order) -> Receipt:
    validated = validate_order(order)
    priced = apply_discounts(validated)
    taxed = apply_tax(priced, order.jurisdiction)
    receipt = charge_payment(taxed)
    notify_customer(receipt)
    audit_log.record("order.completed", receipt.order_id)
    return receipt
```

40% of AI code smells are "Long Method." The composed version debugs 3x faster.
