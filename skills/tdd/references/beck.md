# TDD Reference — Kent Beck's Strategies and Patterns

Source: Kent Beck, "Test-Driven Development: By Example" (2002)

## The Two Rules

From Beck:

> 1. Write new code only if an automated test has failed
> 2. Eliminate duplication

These two simple rules generate complex individual and group behavior. They imply an order to the tasks of programming that produces "clean code that works."

## The Rhythm: Red/Green/Refactor

### Red — Write a failing test

Write a test that doesn't work, perhaps doesn't even compile at first. Think about how you would like the operation to appear in your code. You are writing a story. Invent the interface you wish you had.

When writing the test:
- Start with the assert first — what is the right answer?
- Work backward to discover what objects and messages you need
- Use data that makes tests easy to read and follow

### Green — Make it work quickly

Getting the bar green dominates everything else. If a clean, simple solution is obvious, type it in. If not, commit whatever sins necessary to make the test pass. Speed trumps design, just for this moment.

### Refactor — Eliminate duplication

Now that the system is behaving, put the sinful ways behind you. The duplication between test and code must be eliminated. This is where design emerges.

The key insight: The constant in your fake implementation (e.g., `return 10`) came from somewhere — it's duplicated in your test (`assertEquals(10, ...)`). Eliminating this duplication drives you toward the real implementation.

## Why TDD Works

TDD manages fear during programming. Fear makes you tentative, makes you want to communicate less, makes you shy away from feedback. The tests are a ratchet — once you get one test working, you know it works now and forever. The tougher the problem, the less ground each test should cover.

## The Design Paradox

By not considering the future of your code, you make your code much more likely to be adaptable in the future.

What happens in practice:
1. First feature goes in simply and straightforwardly
2. Second feature (a variation) goes in — duplication gets factored into one place, differences into different places
3. Third feature finds common logic reusable, unique logic has obvious home

The Open/Closed Principle gets satisfied naturally, for precisely those variations that occur in practice.

## Strategies for Getting to Green

### Fake It ('Til You Make It)

Return a constant, then gradually transform it into an expression using variables.

```
return "1 run, 0 failed"
  becomes
return "%d run, 0 failed" % self.runCount
  becomes
return "%d run, %d failed" % (self.runCount, self.failureCount)
```

Why Fake It is powerful:
- **Psychological** — Green bar feels different from red bar
- **Scope control** — Prevents prematurely confusing yourself with extraneous concerns

### Triangulation

When truly unsure about abstraction, write tests with different values:

```
assertEquals(4, plus(3, 1))
assertEquals(7, plus(3, 4))
```

Only when you have two examples do you generalize. This prevents premature abstraction.

### Obvious Implementation

When confident in the solution and recent tests have all passed first try, type in the real implementation directly.

If you get an unexpected red bar after Obvious Implementation, you were overconfident. Back off to Fake It.

### One to Many

Implement for collections by first implementing for one item:

1. Write `sum(5)` returning 5
2. Add parameter `sum(5, [5])`
3. Use collection instead of single value
4. Delete single value parameter
5. Enrich test to `sum([5, 7])`

## Step Size

You should be able to work in teeny-tiny steps when needed. TDD is not about taking small steps — it's about being able to take small steps when the situation calls for it.

- When everything is going smoothly: Obvious Implementation
- When you get unexpected red bars: back off to Fake It
- When really stuck: Triangulate

If you only know how to take big steps, you'll never know if smaller steps are appropriate.

## Test Selection

Which test to write next? Pick a test that will teach you something and that you are confident you can implement. The "obvious" ones provide no learning. The "have no idea" ones provide too much risk. Find the one that isn't obvious but you can make work.

Starter Test: Begin with something that doesn't do anything yet. A polygon reducer? Start with an empty list of polygons returning an empty list.

## What to Test

Write tests until fear is transformed into boredom. Test conditionals, loops, operations, polymorphism — but only those that you write. Don't test code from others unless you have reason to distrust it.

## Isolated Tests

Tests should be able to ignore one another completely. If one test breaks, you want one problem. If two tests break, you want two problems.

This encourages you to compose solutions out of many highly cohesive, loosely coupled objects. Isolating tests is how you actually achieve "loose coupling" regularly.

## Value Objects and Immutability

Value Objects are a response to aliasing problems. If you have an object and you know it won't change, you can pass around references without worrying about hidden changes.

When implementing a Value Object:
- Every operation returns a fresh object, leaving the original unchanged
- Users must store the result
- Implement equality (two $5 bills should be equal)

## Mock Objects

Create fake versions of expensive/complicated resources that return constants:

```
MockDatabase.expectQuery("select...")
MockDatabase.returnResult(["Order 2", "Order 3"])
```

Mock Objects:
- Give you performance and reliability
- Improve readability (you can see why 14 is the expected answer)
- Encourage careful consideration of object visibility
- Reduce coupling in designs

The risk: What if the mock doesn't behave like the real object? Have tests for the mock that can also run against the real object.

## Signs Tests Are in Trouble

Tests reveal design problems:

- **Long setup code** — Objects are too big and need to be split
- **Setup duplication** — Too many objects too tightly intertwined
- **Long running tests** — Testing bits and pieces is hard (design problem)
- **Fragile tests** — One part surprisingly affecting another

## When Tests Fail Unexpectedly

1. Write a smaller test case that fails
2. Make that test work
3. Return to the original test — it will probably work now

## When to Delete Tests

Never delete a test that exercises a different path through the code. But if two tests are redundant (they'd both break from the same defect insertion), delete the less readable one.

## The Feedback Loop

TDD is an awareness of the gap between decision and feedback during programming, and techniques to control that gap.

- Want more safety? Smaller steps, more tests
- Feeling confident? Bigger steps, Obvious Implementation
- Got an unexpected red bar? Back off, smaller steps

## Clean Code That Works

That's the goal. Not clean code. Not code that works. Clean code that works.

- It's predictable — you know when you're finished
- It gives you a chance to learn all lessons the code teaches
- It improves lives of users
- It lets teammates count on you

## Sources

- Kent Beck, "Test-Driven Development: By Example" (2002)
- Kent Beck, "Extreme Programming Explained" (2000, 2nd ed. 2004)
