---
title: Test Edge Cases
impact: CRITICAL
impactDescription: Prevents production failures
tags: testing, boundaries, edge-cases
---

## Test Edge Cases

The happy path is easy. The edges are where production breaks.

**❌ (only tests the obvious case):**

```
function test_divide():
    assert divide(10, 2) == 5
```

**✓ (covers boundaries and failure modes):**

```
function test_divide():
    assert divide(10, 2) == 5
    assert divide(7, 2) == 3.5
    assert divide(0, 5) == 0
    assert throws(DivideByZero) { divide(5, 0) }
    assert throws(TypeError) { divide("10", 2) }
```

EvalPlus benchmark: AI pass rates drop 19-29% on boundary tests — the 20% that breaks production.
