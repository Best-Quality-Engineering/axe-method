---
name: tdd
description: Test-Driven Development discipline — Red/Green/Refactor cycle with mandatory test-first workflow. Principles from Kent Beck. Use when writing new features, fixing bugs, or when you want tests to guide design decisions rather than verify after the fact.
---

# Test-Driven Development

Write new code only if an automated test has failed. Eliminate duplication. These two rules generate clean code that works.

## Hard Rules

- **No production code without a failing test.** The test is the permission slip to write code.
- **No skipping Refactor.** After Green, eliminate duplication between test and code before moving on. This is where design emerges.
- **Test-first is mandatory.** If you wrote production code without a failing test, delete it and write the test.
- **Every component has a test.** No production type exists without at least one test exercising its behavior.
- **Every code change is covered by a test.** If you modify production code, there must be a test that would fail if that change were reverted.

## Protocol

### Before Writing ANY Production Code

HALT. Ask:
- "Do I have a failing test that demands this code?"
- If NO: Write the test first.
- If YES: Proceed, but only write enough to pass THIS test.

### When Starting to Implement

HALT. Ask:
- "Am I typing the real implementation immediately?"
- If YES: Stop. Fake It instead. Return a constant.
- The constant came from the test. That duplication drives the real implementation.

### After the Test Passes (Green)

HALT. Do NOT move to the next feature. Ask:
- "What duplication exists between my test and my code?"
- "What constant in my code is duplicated from my test?"
- Eliminate that duplication. This is where design happens.

### When Tests Fail Unexpectedly

HALT. Do NOT debug harder. Ask:
- "Am I taking too big a step?"
- Back off. Write a smaller test. Get that green first.
- Return to the bigger test later.

### When Tests Are Painful to Write

HALT. The pain is information. Ask:
- "Is setup code getting long?" Objects are too big, need splitting.
- "Am I duplicating setup across tests?" Objects too tightly coupled.
- "Is the test fragile?" Hidden dependencies between components.

Test pain reveals design problems. Fix the design, not the tests.

## Step Size Calibration

| Confidence | Step Size | Approach |
|------------|-----------|----------|
| High, tests passing | Large | Obvious Implementation |
| Uncertain but progressing | Medium | Fake It, then Refactor |
| Stuck or surprised | Small | Triangulate, baby steps |
| Completely lost | Tiny | Test the simplest possible case |

The skill is adjusting step size based on feedback, not always taking small steps.

## Test List Discipline

Before implementing a feature, create an explicit test list:

```
Tests to write:
- [ ] Empty input returns empty output
- [ ] Single item returns that item
- [ ] Two items returns their combination
- [ ] Error case handling
- [ ] ...
```

Pick the next test that:
1. Teaches you something (not obvious)
2. You are confident you can implement (not impossible)

Cross off tests as completed. Add new tests as discovered.

## Red Flags

Signs you are not doing TDD:

1. **Production code exists without a failing test.** Delete the code. Write the test.
2. **Debugging instead of writing smaller tests.** Stop debugging. Write a smaller test that isolates the problem.
3. **Designing in your head before testing.** Write a test that expresses what you want, then discover the design.
4. **Skipped refactoring because "it works."** Go back. Find the duplication. Eliminate it.
5. **Tests feel like a chore after implementation.** You did it backwards. Test first next time.
6. **Mocking everything.** Design problem. Too much coupling. Simplify the design.

## Verification

- **Defect insertion:** Change the meaning of a line of production code. A test must break. If none does, write a new test.
- **Coverage invariant:** Every production type has at least one test. Every code change has a covering test.
- **Test naming:** Test names read as specifications of behavior. No "correctly", "properly", "appropriately" — these words hide the actual specification.

## Calibration

### Fake It Protocol

```
// Test: assertEquals(10, calculator.add(5, 5))

// Step 1 — Fake It
function add(a, b) {
  return 10;
}

// Step 2 — See the duplication: 10 in test AND code

// Step 3 — Eliminate duplication
function add(a, b) {
  return a + b;
}
```

Use Obvious Implementation only when confident and recent tests have all passed first try. If an unexpected red bar appears after Obvious Implementation, back off to Fake It.

### Triangulation Protocol

```
// First test
assertEquals(4, plus(3, 1))
// Fake It: return 4

// Second test with different values
assertEquals(7, plus(3, 4))
// Now MUST generalize: return a + b
```

Triangulation prevents premature abstraction. Only generalize when forced by multiple examples.

### Three Ways to Green

| Strategy | When to Use |
|----------|-------------|
| **Fake It** | Default. Return a constant, then eliminate duplication. |
| **Obvious Implementation** | Confident in the solution and recent tests all pass. |
| **Triangulate** | Unsure how to generalize. Add examples until forced. |
