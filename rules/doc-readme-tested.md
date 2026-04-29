---
title: READMEs with Tested Steps
impact: HIGH
impactDescription: Working onboarding
tags: documentation, readme, developer-experience
---

## READMEs with Tested Steps

A README is what someone reads at 2am when production is on fire.

**❌ (structure without substance):**

```
# MyApp

A comprehensive, robust solution for modern web development.

## Features
- Easy to use
- Fast and scalable

## Getting Started
    git clone <repo>
    npm install
    npm start
```

**✓ (tested steps, troubleshooting, known issues):**

```
# MyApp

Payment webhook processor. 5k Stripe events/min at peak.

Requires: Node.js 20+, Redis 7+

    npm install
    cp .env.example .env
    npm run migrate
    npm run dev

## Troubleshooting

| Symptom              | Cause                | Fix                      |
|----------------------|----------------------|--------------------------|
| 401 on /webhooks     | Wrong signing secret | `stripe listen` shows it |
| Events not processing| Redis not running    | `redis-cli ping` → PONG  |

## Known Issues
- Webhook verification fails behind some proxies — [workaround](docs/proxy.md)
```

Bad READMEs cost hours — one team lost a full day to an undocumented env var. Ship working steps and a troubleshooting table; skip the boilerplate.
