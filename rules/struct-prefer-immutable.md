---
title: Prefer Immutable Data
impact: MEDIUM
impactDescription: Fewer state bugs
tags: structure, immutability, pure-functions
---

## Prefer Immutable Data

Same input → same output → no 3am debugging sessions chasing phantom side effects.

❌ (mutable shared state):

```
class OrderBuilder:
    total = 0

    function add_item(item):
        if item.price < 0:
            return
        this.total += item.price

    function get_total():
        return this.total
```

✓ (pure function, new state):

```
function add_item(order: Order, item: Item) -> Order:
    assert item.price >= 0, "Invalid price: {item.price}"
    return Order(
        items = append(order.items, item),
        total = order.total + item.price
    )
```

Shared mutable state is the #1 source of heisenbugs. Pure functions eliminate that entire category.
