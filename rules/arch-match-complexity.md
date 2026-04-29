---
title: Match Solution Complexity to Problem
impact: HIGH
impactDescription: Avoids over-engineering
tags: architecture, simplicity, abstractions
---

## Match Solution Complexity to Problem

Complexity should match the problem, not the pattern catalog.

**❌ (90 lines of abstraction for a 1-line task):**

```
config = ConfigReaderFactory()
    .create(Format.YAML)
    .with_strategy(...)
    .read("app.yaml")
```

**✓ (direct, proportional to complexity):**

```
config = yaml.parse_file("app.yaml")
```

Microsoft's data: AI code runs 20-30% more verbose because it picks patterns by frequency, not judgment.
