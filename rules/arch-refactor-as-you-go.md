---
title: Refactor As You Go
impact: HIGH
impactDescription: Prevents architectural decay
tags: architecture, refactoring, technical-debt
---

## Refactor As You Go

Every 2-3 features, consolidate — or you end up with an 85-line function hiding four unrelated concerns.

**❌ (accumulated complexity):**

```
function process_payment(order):
    // Original: 20 lines
    // Feature 1: +15 lines for discounts
    // Feature 2: +20 lines for tax
    // Feature 3: +30 lines for multi-currency
    // 85 lines, 4 concerns
```

**✓ (composed from single-purpose functions):**

```
function process_payment(order) -> Receipt:
    priced = pricing.apply_discounts(order)
    taxed = tax.apply(priced, order.jurisdiction)
    converted = currency.convert(taxed, order.currency)
    return payment.charge(converted)
```

Refactoring activity dropped 60% since AI adoption. New code grows; architecture rots.
