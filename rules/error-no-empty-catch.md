---
title: No Empty Catch Blocks
impact: CRITICAL
impactDescription: ~20% fewer missed errors
tags: error-handling, exceptions, logging
---

## No Empty Catch Blocks

`logger.info(e)` writes to a log file that likely has no alerting. ~20% of production errors die silently in catch blocks like this.

❌

```
try {
    charge_payment(order)
} catch (e) {
    logger.info(e)
}
```

✓

```
try {
    charge_payment(order)
} catch (e) {
    if e is PaymentDeclined:
        mark_order_failed(order, e.reason)
        return Result.err(PaymentFailed(e.reason))
    throw e
}
```

RLVR training loops reinforce the pattern — models optimize for "files successfully processed" by swallowing exceptions and continuing.
