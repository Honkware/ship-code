# Ship Code

Code quality guidelines for shipping production-grade software. Twenty rules across seven categories.

## When to Apply

Reference these guidelines when:
- Writing or editing code, tests, documentation, or configuration
- Reviewing AI-generated output before committing
- Starting new projects or adding features
- Refactoring existing code
- Any time output needs to survive a code review

## Rule Categories by Priority

| Priority | Category | Prefix |
|----------|----------|--------|
| 1 | Error Handling & Resilience | `error-` |
| 2 | Security & Dependencies | `sec-` |
| 3 | Testing & Verification | `test-` |
| 4 | Architecture & Abstraction | `arch-` |
| 5 | Documentation & Communication | `doc-` |
| 6 | Naming & Readability | `name-` |
| 7 | Code Structure | `struct-` |

---

## Separate Business Logic from I/O

Business logic stays pure; handlers stay thin.

**❌ (handler does everything):**

```
route "/report":
    user = db.raw_query("SELECT ...", [session.user_id])
    if not user.is_admin and query.scope == "all":
        return 403
    data = db.raw_query("... complex SQL ...")
    return json(format_for_frontend(data))
```

**✓ (thin adapter, pure business logic):**

```
function generate_report(scope, user) -> Report:
    assert can_view(user, scope), "Unauthorized"
    return report_repo.generate(scope)

route "/report":
    return json(generate_report(query.scope, current_user))
```

80% architectural violation rate in LLM-generated code — "Hallucinated Coupling."

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

---

## No Code Duplication

Copy-paste is a bug multicast protocol — one mistake, many destinations.

**❌ (same validation copied everywhere):**

```
create_order(body):
    if body is null or body.items is null:
        return 400, "Items required"

create_invoice(body):
    if body is null or body.items is null:
        return 400, "Items required"
```

**✓ (shared utility, single source of truth):**

```
function require_fields(body, fields):
    missing = fields.filter(f => body[f] is null)
    assert missing.is_empty(), "Missing: {missing}"

create_order(body):
    require_fields(body, ["items"])

create_invoice(body):
    require_fields(body, ["items"])
```

GitClear: copy-pasted code rose from 8.3% to 12.3% since AI adoption; refactoring dropped from 25% to <10%.

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

---

## Strip AI Documentation Tells

Mood words are a substitute for specifics.

**❌ (AI-generated filler):**

```
Let's dive into the comprehensive guide for this robust application.
It's important to note that you'll need Node.js installed. Furthermore,
this cutting-edge solution leverages modern technologies to deliver a
seamless experience.
```

**✓ (specific, actionable):**

```
Prerequisites: Node.js 20+ (`node --version`)

Install:
    git clone <repo>
    npm install
    npm run migrate
    npm run dev       # http://localhost:3000
```

28x frequency spike for "delve" in AI text. Replace mood with detail: "robust system" → "handles 10k req/s without dropping data." "It's important to note that" → just state the fact.

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

---

## Fail Fast and Loud

`return null` from a catch block doesn't communicate failure — it communicates "no result found." 47 production bugs traced back to this exact pattern.

❌

```
function get_user(id):
    try:
        return db.query(id)
    catch any:
        return null
```

✓

```
function get_user(id) -> User:
    user = db.query(id)
    if user is null:
        raise UserNotFoundError("User {id} not found")
    return user
```

Martin Fowler, IEEE Software: "Fail fast does exactly the opposite of fail-safe: when a problem occurs, it fails immediately and visibly."

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

---

## Consistent Vocabulary

`get_user` and `fetch_customer` — same function, two names, twice the cognitive load.

❌ (same concept, three names):

```
function get_user(id)
function fetch_customer(id)
function retrieve_client(id)
```

✓ (consistent pattern, learned once):

```
function get_user(id)
function get_user_by_email(email)
function get_user_preferences(id)
```

Boring consistency beats creative variety every time. Learn the pattern once, read fluently everywhere.

---

## Names That Reveal Intent

`data`, `result`, `handler` — you read ten more lines just to find out what any of them actually do.

❌ (generic, needs context):

```
data = fetch()
result = process(data)
handler = Handler()
valid = check(x)
```

✓ (self-describing):

```
pending_orders = order_repo.fetch_pending()
invoice_totals = pending_orders.map(calculate_grand_total)
payment_gateway = PaymentGateway(api_key)
tax_id_is_valid = tax_authority.validate_format(tax_id)
```

Developers spend 60% of code review time decoding vague names. Good ones are free documentation.

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

---

## Validate All External Input

Veracode 2025 tested 100+ LLMs: 86% failed XSS prevention, 88% failed log injection. The models skipped validation entirely.

❌

```
function get_user(id):
    return db.raw_query("SELECT * FROM users WHERE id = {id}")
```

✓

```
function get_user(id: UUID) -> User:
    assert id.is_valid(), "Invalid user ID"
    return users.find_by_id(id)
```

HTTP params, files, env vars, external APIs — every entry point is an injection surface.

---

## Verify Dependencies Before Installing

Check it exists, check the date, check for CVEs. When in doubt: stdlib.

**❌ (installing a hallucinated package name):**

```
// Hallucinated package — does not exist
npm install express-mongoose-auth-utils
```

**✓ (verification checklist):**

```
// 1. Verify on official registry
// 2. Check download count, last update, known CVEs
// 3. Review for postinstall scripts
// 4. Pin exact version
// 5. Run security audit after adding
// When in doubt: use the standard library
```

19.7% of AI-recommended packages are hallucinated (USENIX Security 2025); attackers register those names and inject malware — "slopsquatting."

---

## Domain-Based Directory Structure

`controllers/`, `services/`, `models/` — you see the frameworks, not the system.

❌ (technical layers obscure intent):

```
src/
    controllers/
    services/
    models/
    utils/
    helpers/
```

✓ (business domains reveal intent):

```
src/
    order/
        model/
        pricing/
        tax/
    payment/
        gateway/
        receipt/
    user/
        auth/
        profile/
```

New developers understand domain-organized codebases in half the time — the tree tells them what the system does, not which frameworks it uses.

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

---

## Single Purpose Functions

Extract until the parent function reads like a summary.

❌ (80-line function, 6 concerns):

```
function handle_order(order):
    // validate (15 lines)
    // price with discounts (20 lines)
    // calculate tax (15 lines)
    // process payment (10 lines)
    // send email (10 lines)
    // audit log (10 lines)
```

✓ (composed from named steps):

```
function process_order(order) -> Receipt:
    validated = validate_order(order)
    priced = apply_discounts(validated)
    taxed = apply_tax(priced, order.jurisdiction)
    receipt = charge_payment(taxed)
    notify_customer(receipt)
    audit_log.record("order.completed", receipt.order_id)
    return receipt
```

40% of AI code smells are "Long Method." The composed version debugs 3x faster.

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
