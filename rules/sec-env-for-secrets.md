---
title: Secrets in Environment Variables
impact: CRITICAL
impactDescription: Prevents credential leaks
tags: security, secrets, configuration
---

## Secrets in Environment Variables

GitGuardian found ~29 million secrets on GitHub in 2025. AI-assisted commits leaked at 2x the baseline rate.

❌

```
API_KEY = "sk-live-abc123"
DB_PASSWORD = "hunter2"
```

✓

```
API_KEY = env.get("API_KEY")
DB_PASSWORD = env.get("DB_PASSWORD")
assert API_KEY != null, "API_KEY required"
assert DB_PASSWORD != null, "DB_PASSWORD required"
```

`assert` at startup means the app dies immediately with a clear message — not a cryptic failure three requests later.
