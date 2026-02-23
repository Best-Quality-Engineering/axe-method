---
name: refactoring
description: Pattern-directed refactoring with blast radius discovery. Traces all callers, implementations, tests, and usages before making changes. Principles from Martin Fowler, Joshua Kerievsky. Use when refactoring code, renaming symbols, extracting classes/methods/interfaces, or making structural changes that could affect multiple files.
---

# Refactoring

Before ANY refactoring, discover the blast radius. Find all code that will be affected by the change BEFORE making the change.

## Hard Rules

- **Always trace symbols before changing.** Run blast radius discovery for the refactoring pattern being applied.
- **Always report findings before making changes.** Present what you found to the user before modifying anything.
- **Always run tests after changing.** Confirm nothing broke.

## Protocol

### 1. Declare the Refactoring

When a refactoring is requested:

```
1. IDENTIFY the refactoring pattern (from catalog below)
   |
2. EXECUTE blast radius discovery for that pattern
   |
3. REPORT findings before making changes
   |
4. APPLY changes systematically across all affected code
   |
5. RUN tests to verify
```

### 2. Trigger Phrases

This skill activates for:
- "Rename X to Y"
- "Extract interface from this class"
- "Move this function to..."
- "Extract method from this code"
- "Replace inheritance with composition"
- "Refactor this to use the Strategy pattern"

## Blast Radius Discovery

### Universal Symbol Tracing (Always Do First)

For ANY refactoring involving a symbol:

1. Find all references to the symbol in source code
2. Find import/require statements
3. Find test files referencing the symbol
4. Find type annotations using the symbol
5. Find in documentation (`.md`, `.mdx`, `.rst`, `.adoc`)
6. Find in Storybook stories
7. Find in GraphQL schemas and operations
8. Find in Infrastructure as Code
9. Find in CI/CD configurations

### Language-Specific Tools

When available, prefer semantic tools over text search:

| Language | Tool |
|----------|------|
| TypeScript | `ts-prune`, LSP `textDocument/references` |
| Java | LSP, IntelliJ references |
| Python | `jedi`, `pyright` references |
| Go | `gopls` references |
| Swift | Xcode refactoring, SourceKit |

## Core Pattern Catalog

### Extract Function/Method

Pull code fragment into its own named function.

**Blast radius:**
- Variables used in extracted code (become parameters or stay in scope?)
- Variables modified in extracted code (need to return?)
- Early returns / control flow that affects calling code
- Exception handling that might change behavior

### Inline Function/Method

Replace function call with function body.

**Blast radius:**
- ALL callers of the function (each gets the body inlined)
- Different behavior based on call context?
- Side effects that change meaning when inlined?
- Tests that mock or stub this function

### Rename Symbol

Change name of variable, function, class, or type. A rename has two distinct blast radii:

**Phase 1 — Direct references:** Code that uses the symbol (type annotations, imports, calls, string literals, config files, API contracts, documentation).

**Phase 2 — Derived symbols:** Other symbols whose names incorporate the concept being renamed. These are not references — they are independently named things whose names were derived from the renamed concept.

| Category | Example (renaming `FooBar` to `FooBaz`) |
|----------|----------------------------------------|
| Test suites/files | `FooBarTests` to `FooBazTests` |
| Test method names | `testFooBarHandlesX` to `testFooBazHandlesX` |
| Mock/stub/fake types | `MockFooBar` to `MockFooBaz` |
| Helper types | `FooBarBuilder`, `FooBarFactory` |
| Property names | `let fooBar: FooBaz` to `let fooBaz: FooBaz` |
| Parameter names | `init(fooBar:)` to `init(fooBaz:)` |
| File names | `FooBar.swift` to `FooBaz.swift` |
| MARK comments | `// MARK: - FooBar` |
| Doc comments | Prose references to the concept |
| Assertion messages | Error strings mentioning the concept |

**Decision rule:** If the derived symbol would have been named differently had the original symbol always had its new name, it should be renamed. If the derived symbol has its own independent identity, leave it.

**Conceptual decomposition:** When a rename changes a concept (not just a prefix), decompose the old name into parts, identify which part changed, and search for the changed part at each level of specificity. This catches mocks, helpers, and shorthands that use the concept without the full qualifier.

Present derived symbols to the user in a table before proceeding:

```
Derived symbols found — confirm which should be renamed:

| Current Name | Proposed Name | Rename? |
|-------------|--------------|---------|
| FooBarTests | FooBazTests | ? |
| MockFooBar | MockFooBaz | ? |
```

### Move Function

Move function to another module/class.

**Blast radius:**
- All callers (need import update)
- All callees (can function reach them from new location?)
- `this` / `self` references (context change)
- Circular dependency created?
- Test imports

### Move Field

Move field from one class to another.

**Blast radius:**
- All reads and writes of the field
- Accessor methods (getters/setters)
- Serialization (JSON, ORM mappings)
- Subclasses that use the field
- Tests accessing the field

### Extract Class

Create new class from subset of fields/methods.

**Blast radius:**
- All methods and fields being moved
- References from remaining methods to extracted members
- References from other classes to extracted members
- Subclasses depending on extracted members
- Tests instantiating or mocking the original class

### Extract Interface

Create interface from class's public methods.

**Blast radius:**
- All type annotations using the concrete class
- All instantiation sites (might stay as concrete)
- Factory methods returning the type
- Generic constraints using the type
- Test mocks (should mock interface, not concrete)
- Dependency injection bindings

## Extended Blast Radius: Non-Code Artifacts

Beyond source code, symbols appear in documentation, infrastructure, and tooling. For the full checklist of artifact categories, see [references/extended-blast-radius.md](references/extended-blast-radius.md).

## Refactoring to Patterns

Higher-level refactorings that introduce design patterns (Factory, Strategy, State, Composite, Observer, Null Object, etc.). For the full Kerievsky catalog with mechanics and blast radius for each, see [references/pattern-catalog.md](references/pattern-catalog.md).

Note: The Singleton pattern in the catalog generally conflicts with IoC/DI principles. Prefer dependency injection over Singleton for managing shared instances.

## Execution Checklist

1. **Identify pattern.** Which refactoring am I applying?
2. **Run blast radius discovery.** Execute the checklist for that pattern.
3. **Check extended artifacts.** Documentation, IaC, CI/CD, GraphQL, Storybook.
4. **Report findings.** Tell the user what I found before changing anything.
5. **Verify tests exist.** Are there tests covering the affected code?
6. **Make changes systematically.** File by file, verify compilation.
7. **Regenerate derived files.** GraphQL codegen, OpenAPI types, etc.
8. **Run tests.** Confirm nothing broke.
9. **Update documentation.** Sync docs if needed.
10. **Report completion.** Summary of all files changed.

## Verification

- All tests pass after the refactoring.
- All references updated (no stale imports, no broken type annotations).
- Documentation synced with code changes.
- No new circular dependencies introduced.
- Derived symbols evaluated and renamed where appropriate.
