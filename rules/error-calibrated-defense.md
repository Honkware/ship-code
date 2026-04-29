---
title: Calibrated Error Defense
impact: CRITICAL
impactDescription: Right-sized error handling
tags: error-handling, assertions, validation
---

## Calibrated Error Defense

Network timeouts get retries. Invalid arguments get assertions. Liu et al. (2025) found 35-43% of LLM-generated code miscalibrates this split.

❌

```
function calculate(price, discount):
    try
        return price * (discount / 100)
    catch any
        return 0
```

✓

```
function calculate(price: Money, discount: Percent) -> Money:
    assert price >= 0, "Price must be non-negative, got {price}"
    assert 0 <= discount <= 100, "Discount must be 0-100, got {discount}"
    return price * (discount / 100)
```

External problems get handled. Internal contract violations get assertions.
