---
title: Test Behavior, Not Coverage
impact: CRITICAL
impactDescription: Catches real bugs
tags: testing, assertions, quality
---

## Test Behavior, Not Coverage

Expected values come from the spec, not the implementation.

**❌ (tautological, verifies nothing):**

```
function test_calculate_total():
    result = calculate_total([item1, item2])
    assert result is not null
    assert result == calculate_total([item1, item2])
```

**✓ (expected value from spec):**

```
function test_applies_ten_percent_discount():
    result = calculate_total(
        items = [Item(price = 100), Item(price = 50)],
        discount = 10
    )
    assert result == 135.00
```

95% line coverage, 4% mutation score — the "cardboard muffins" problem: hardcoding the implementation's return value means both share the same bug.
