---
title: Comments Explain Why, Not What
impact: HIGH
impactDescription: Useful comments
tags: documentation, comments, communication
---

## Comments Explain Why, Not What

Comments are for trade-offs, failures, and context the code can't express.

**❌ (restates the obvious):**

```
// Increment the counter by 1
counter += 1

// Loop through the users
for user in users:
    process(user)
```

**✓ (preserves context code cannot express):**

```
// Compensate for off-by-one in legacy API (0-indexed pages)
counter += 1

// Stream in batches — loading the full table caused OOM
// (incident #2847). Keep batch_size <= 1000.
for user in repo.stream_batch(size = 1000):
    process(user)
```

"If a name requires a comment, the name does not reveal its intent" — Clean Code.
