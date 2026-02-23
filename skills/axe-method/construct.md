# Construct

The Construction phase builds from specifications following TDD discipline. Tests are derived from specs, not from code. The construction agent reads the blueprints, sequences the work, and delivers.

## When to Use

- Specs exist and are ready to be built against
- User says `/axe-method:construct`
- After completing the Specify phase

## Available Agents

| Agent | Role |
|-------|------|
| `axe-method:constructor` | Primary construction agent — inventories specs, identifies toolchain, sequences work, derives tests (Red), implements code (Green), delivers the finished product |
| `axe-method:engineering-specifier` | Reference for implementation details, UI specs, and toolchain |
| `axe-method:inspector` | Quick conformance checks during construction |

The `axe-method:constructor` is the lead agent for this phase. It preloads the `axe-method:axe-method` skill and uses project-level persistent memory.

For larger construction efforts, spawn an agent team with the construction agent coordinating teammates that own different spec areas.

## The Process

### Step 1: Inventory Specs

Use Task (Explore) to scan `Documents/Specifications/` for all spec files in the target domain. Build a complete list of behaviors that need implementation.

**Use AskUserQuestion:**
```
"I found these specs for {Domain}:
  - Experience: [list of behaviors]
  - Architecture: [list of structural decisions]
  - Engineering: [list of implementation specs]

Which area should we construct first?"
  Options: Start from Experience behaviors / Start from Engineering specs / Let me choose
```

### Step 2: Derive Tests (Red)

For each spec behavior, derive test assertions at ALL applicable levels. Every stated behavior becomes tests — plural.

1. **Read the spec behavior** — Quote the exact requirement
2. **Determine ALL applicable test levels** — A user-facing behavior needs UI tests AND snapshot tests AND unit tests. Coverage at one level does not satisfy coverage at another. List every applicable level before writing any test.
3. **Write tests at every level** — Start from the most user-facing level (UI/E2E), then snapshot, then unit. Each test asserts the spec behavior, not the implementation.
4. **Run the tests** — They should fail (Red). If they pass, either the behavior is already implemented or the tests don't assert correctly.

**Use AskUserQuestion if ambiguous:**
```
"This spec behavior needs tests at multiple levels:
  - '{behavior}'

Applicable levels:
  - UI test: {what it verifies at this level}
  - Snapshot test: {what it verifies at this level}
  - Unit test: {what it verifies at this level}

Proceed with all levels?"
  Options: All levels / Subset (explain why) / Let me decide
```

The test for a correct spec-derived test: **would it fail if the behavior were reverted, but pass for any correct implementation?**

### Step 3: Implement (Green)

Write the minimum code to make the failing tests pass.

1. **Follow the Architecture spec** — Respect boundaries, seams, layers, dependency direction
2. **Follow the Engineering spec** — Use the prescribed protocols, data structures, and UI implementation
3. **Run the test** — It should pass (Green)
4. **Move to the next behavior**

If a spec is too vague to produce a test, that is a signal — note it for the next Specify iteration. Do not guess; flag the gap.

### Step 4: Track Progress

Use TaskCreate to track each behavior being constructed:

- Create a task per spec behavior: "Implement {behavior}"
- Mark in_progress when deriving tests
- Mark completed when tests pass
- Create new tasks for any gaps or ambiguities discovered

**Use AskUserQuestion at milestones:**
```
"Construction progress for {Domain}:
  - Completed: [N] behaviors
  - In progress: [N] behaviors
  - Gaps found: [N] (spec too vague to test)

Continue to next area, or review gaps first?"
  Options: Continue / Review gaps / Run full test suite / Move to Inspection
```

## After Construct

When all spec behaviors have passing tests (or gaps are documented), the next step is **Inspection** — evaluating the product against intent. Invoke `/axe-method:inspect`.
