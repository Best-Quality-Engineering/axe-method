---
name: clean-code
description: Clean Code discipline — writing code that can be understood immediately by everyone on the team. Principles from Robert C. Martin et al. Use when writing new code, reviewing code, refactoring, or when code feels hard to read or maintain.
---

# Clean Code

Code is clean if it can be understood easily — by everyone on the team. The measure: would a stranger understand this immediately? If you have to explain it, it isn't clean.

## Hard Rules

### Naming
- No single-letter variables except `i`, `j`, `k` in loops under 5 lines.
- No abbreviations unless universally understood (`url`, `id`, `html`).
- No generic names: `data`, `info`, `temp`, `result`, `thing`, `item`, `obj`.
- Class names are nouns. Method names are verbs.
- One word per concept, used consistently (`fetch`, `retrieve`, `get` — choose one).

### Functions
- No function exceeds 20 lines. Extract until it doesn't.
- No more than 3 parameters. Use a parameter object beyond that.
- No flag parameters. Split into separate functions.
- No `and` in function names. That's two functions.
- One level of abstraction per function. Don't mix high-level policy with low-level detail.

### Comments
- No comments explaining WHAT. Rename until unnecessary.
- Only comments explaining WHY. And only when code can't express it.
- No commented-out code. Source control remembers.
- No closing brace comments. If blocks are that long, extract functions.

### Error Handling
- No returning null from methods. Return empty collection, Optional, or throw.
- No passing null as arguments. Design APIs that don't accept null.
- No naked try-catch that swallows exceptions. Always handle or propagate meaningfully.
- Separate business logic from error handling. A function that handles errors does nothing else.

### Boundaries
- No direct use of third-party types in core domain. Wrap external APIs.
- No spreading framework annotations throughout business logic. Keep frameworks at edges.

### Structure
- No line exceeds 100 characters.
- No duplicate logic. Extract to shared function immediately.
- No negative conditionals when positive works: `if (isValid)` not `if (!isInvalid)`.

## Protocol

After generating code, execute this sequence:

```
1. SCAN for hard rule violations
   |
2. FIX each violation
   |
3. READ the code as a stranger would
   |
4. ASK: "Would someone understand this immediately?"
   |
   If NO: Refactor names, extract functions, simplify
   If YES: Done
```

## Design Rules

- Prefer polymorphism to if/else or switch/case. Let objects handle their own behavior.
- Use dependency injection. Dependencies should be injected, not created internally.
- Follow Law of Demeter. A class should know only its direct dependencies. Don't chain calls through returned objects.
- Command Query Separation. Functions either do something (command) or answer something (query), not both.
- Keep configurable data at high levels. Constants and configuration belong at the top, not buried in implementation.

## Objects and Data Structures

Objects and data structures are opposites. Choose deliberately.

- **Objects** hide data behind abstractions and expose functions that operate on that data.
- **Data structures** expose data and have no meaningful functions.
- **No hybrids.** Half object and half data structure creates the worst of both worlds.

## Verification

The standard is simple: Would a stranger understand this immediately?

If you have to explain the code, it isn't clean yet. Rename, extract, simplify until the explanation is unnecessary.

## Calibration

### Naming

```
// WRONG
const d = new Date();
const data = fetchUser();
function process(x) { ... }

// RIGHT
const currentDate = new Date();
const activeUser = fetchUser();
function validateEmailFormat(email) { ... }
```

### Functions

```
// WRONG — too many concerns
function saveUser(user, validate, sendEmail, log) {
  if (validate) { /* validation logic */ }
  /* save logic */
  if (sendEmail) { /* email logic */ }
  if (log) { /* logging logic */ }
}

// RIGHT — one thing each
function validateUser(user) { ... }
function persistUser(user) { ... }
function sendWelcomeEmail(user) { ... }
function logUserCreation(user) { ... }
```

### Parameters

```
// WRONG — too many parameters
function createUser(name, email, age, street, city, zip, country) { ... }

// RIGHT — parameter object
function createUser(userData: UserData) { ... }

interface UserData {
  name: string;
  email: string;
  age: number;
  address: Address;
}
```

### Conditionals

```
// WRONG — negative and nested
if (!user.isInactive) {
  if (!user.isUnverified) {
    processUser(user);
  }
}

// RIGHT — positive and flat
if (user.isActive && user.isVerified) {
  processUser(user);
}
```

### Comments

```
// WRONG — explains WHAT
// Loop through users and check if active
for (const user of users) {
  if (user.status === 'active') { ... }
}

// RIGHT — code explains itself
for (const user of users) {
  if (user.isActive()) { ... }
}

// RIGHT — comment explains WHY (when needed)
// Retry limit set to 3 per SLA requirements in contract section 4.2
const MAX_RETRIES = 3;
```

### Function Length

```
// WRONG — doing too much
function processOrder(order) {
  // validate
  if (!order.items) throw new Error('No items');
  if (!order.customer) throw new Error('No customer');

  // calculate totals
  let subtotal = 0;
  for (const item of order.items) {
    subtotal += item.price * item.quantity;
  }
  const tax = subtotal * 0.08;
  const total = subtotal + tax;

  // save
  database.save({ ...order, subtotal, tax, total });

  // notify
  emailService.send(order.customer.email, 'Order confirmed');

  return { success: true, total };
}

// RIGHT — each function does one thing
function processOrder(order) {
  validateOrder(order);
  const totals = calculateOrderTotals(order);
  saveOrder(order, totals);
  notifyCustomer(order);
  return { success: true, total: totals.total };
}

function validateOrder(order) {
  if (!order.items) throw new Error('No items');
  if (!order.customer) throw new Error('No customer');
}

function calculateOrderTotals(order) {
  const subtotal = order.items.reduce(
    (sum, item) => sum + item.price * item.quantity, 0
  );
  const tax = subtotal * 0.08;
  return { subtotal, tax, total: subtotal + tax };
}

function saveOrder(order, totals) {
  database.save({ ...order, ...totals });
}

function notifyCustomer(order) {
  emailService.send(order.customer.email, 'Order confirmed');
}
```
