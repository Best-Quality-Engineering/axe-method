# Code Smells Reference

## Design Smells (Agile PPP)

From Robert C. Martin, "Agile Software Development: Principles, Patterns, and Practices" (2002). These seven design smells indicate structural problems:

### 1. Rigidity
The software is difficult to change. A small change causes a cascade of subsequent changes in dependent modules. The more modules must be changed, the more rigid the design.

### 2. Fragility
The software breaks in many places due to a single change. Often the breakage occurs in areas with no conceptual relationship to the changed area.

### 3. Immobility
You cannot reuse parts of the code in other projects because of involved risks and high effort. The desirable parts are too entangled with undesirable parts.

### 4. Viscosity
Doing things right is harder than doing things wrong. When it's easier to hack a change than to preserve the design, the design has high viscosity.

### 5. Needless Complexity
The design contains elements that are currently not useful. Infrastructure, frameworks, or abstractions that don't serve the current requirements.

### 6. Needless Repetition
The same code appears again and again in slightly different forms. The system is missing an abstraction.

### 7. Opacity
The code is hard to understand. Code that evolved over time tends to become opaque. Constant effort is needed to keep code expressive.

---

## Code Smells (Clean Code ch. 17)

From Robert C. Martin, "Clean Code: A Handbook of Agile Software Craftsmanship" (2008), Chapter 17. Organized by category:

### Comments
- **C1: Inappropriate Information** — Comments that hold information better held in source control, issue tracking, or other systems
- **C2: Obsolete Comment** — A comment that has gotten old, irrelevant, and incorrect
- **C3: Redundant Comment** — A comment that describes something that adequately describes itself
- **C4: Poorly Written Comment** — A comment worth writing is worth writing well
- **C5: Commented-Out Code** — Code that has been commented out. Source control remembers.

### Environment
- **E1: Build Requires More Than One Step** — Building should be a single trivial operation
- **E2: Tests Require More Than One Step** — Running all tests should be a single trivial operation

### Functions
- **F1: Too Many Arguments** — Functions should have few arguments. More than three is very questionable
- **F2: Output Arguments** — Arguments are naturally inputs. Readers don't expect them to be outputs
- **F3: Flag Arguments** — Boolean arguments loudly declare the function does more than one thing
- **F4: Dead Function** — Methods that are never called should be discarded

### General
- **G1: Multiple Languages in One Source File** — Minimize the number of languages in a source file
- **G2: Obvious Behavior Is Unimplemented** — Any function or class should implement the behaviors another programmer could reasonably expect
- **G3: Incorrect Behavior at the Boundaries** — Don't rely on intuition. Look for and test every boundary condition
- **G4: Overridden Safeties** — Turning off failing tests and telling yourself you'll fix them later is as bad as pretending your credit cards are free money
- **G5: Duplication** — Every time you see duplication, it represents a missed opportunity for abstraction. DRY.
- **G6: Code at Wrong Level of Abstraction** — Higher-level general concepts should be in the base class; lower-level detailed concepts should be in derivatives
- **G7: Base Classes Depending on Their Derivatives** — Base classes should know nothing about their derivatives
- **G8: Too Much Information** — Well-defined modules have very small interfaces that allow you to do a lot with a little
- **G9: Dead Code** — Code that isn't executed. Find it and delete it.
- **G10: Vertical Separation** — Variables and functions should be defined close to where they are used
- **G11: Inconsistency** — If you do something a certain way, do all similar things the same way
- **G12: Clutter** — Unused variables, functions that are never called, comments that add no information
- **G13: Artificial Coupling** — Things that don't depend upon each other should not be coupled
- **G14: Feature Envy** — Methods of a class should be interested in the variables and functions of the class they belong to, not other classes
- **G15: Selector Arguments** — Arguments used to select behavior are a sign the function should be split
- **G16: Obscured Intent** — We want code to be as expressive as possible
- **G17: Misplaced Responsibility** — Code should be placed where a reader would naturally expect it
- **G18: Inappropriate Static** — In general, prefer nonstatic methods. When in doubt, make the function nonstatic.
- **G19: Use Explanatory Variables** — Break calculations into intermediate values held in well-named variables
- **G20: Function Names Should Say What They Do** — If you have to look at the implementation to understand what a function does, rename it
- **G21: Understand the Algorithm** — Before you consider yourself done, make sure you understand how it works
- **G22: Make Logical Dependencies Physical** — If one module depends on another, that dependency should be physical, not just logical
- **G23: Prefer Polymorphism to If/Else or Switch/Case** — "ONE SWITCH" rule: there may be no more than one switch for a given type of selection
- **G24: Follow Standard Conventions** — Every team should follow a coding standard
- **G25: Replace Magic Numbers with Named Constants** — In general, it is bad to have raw numbers in your code
- **G26: Be Precise** — Ambiguities and imprecision in code are either a result of disagreements or laziness. Eliminate them.
- **G27: Structure over Convention** — Enforce design decisions with structure rather than convention
- **G28: Encapsulate Conditionals** — Extract functions that explain the intent of a conditional
- **G29: Avoid Negative Conditionals** — Negatives are harder to understand than positives
- **G30: Functions Should Do One Thing** — Functions that do more than one thing should be decomposed
- **G31: Hidden Temporal Couplings** — If functions must be called in a certain order, make the temporal coupling explicit
- **G32: Don't Be Arbitrary** — Have a reason for the way you structure your code
- **G33: Encapsulate Boundary Conditions** — Boundary conditions are hard to keep track of. Put the processing for them in one place.
- **G34: Functions Should Descend Only One Level of Abstraction** — The statements within a function should all be at the same level of abstraction
- **G35: Keep Configurable Data at High Levels** — Configuration constants should live at the highest levels
- **G36: Avoid Transitive Navigation** — A.getB().getC().doSomething() — Law of Demeter violation

### Names
- **N1: Choose Descriptive Names** — Names should describe side effects, actions, or what is returned
- **N2: Choose Names at the Appropriate Level of Abstraction** — Don't pick names that communicate implementation
- **N3: Use Standard Nomenclature Where Possible** — Use existing conventions and patterns
- **N4: Unambiguous Names** — Choose names that make the workings of a function or variable unambiguous
- **N5: Use Long Names for Long Scopes** — The length of a name should be related to the length of the scope
- **N6: Avoid Encodings** — Names should not be encoded with type or scope information
- **N7: Names Should Describe Side-Effects** — Don't hide side effects behind innocent names

### Tests
- **T1: Insufficient Tests** — A test suite should test everything that could possibly break
- **T2: Use a Coverage Tool** — Coverage tools report gaps in your testing strategy
- **T3: Don't Skip Trivial Tests** — They are easy to write and their documentary value is higher than the cost
- **T4: An Ignored Test Is a Question about an Ambiguity** — If requirements are unclear, express that with a commented-out or @Ignored test
- **T5: Test Boundary Conditions** — Pay special attention to boundary conditions
- **T6: Exhaustively Test Near Bugs** — Bugs tend to congregate. When you find a bug, write more tests for that function
- **T7: Patterns of Failure Are Revealing** — Complete test cases, ordered in a reasonable way, expose patterns
- **T8: Test Coverage Patterns Can Be Revealing** — Looking at code that is or isn't executed by passing tests gives clues
- **T9: Tests Should Be Fast** — Slow tests are tests that won't get run

## Sources

- Robert C. Martin, "Agile Software Development: Principles, Patterns, and Practices" (2002) — the seven design smells
- Robert C. Martin, "Clean Code: A Handbook of Agile Software Craftsmanship" (2008), Chapter 17 — the categorized smell catalog
