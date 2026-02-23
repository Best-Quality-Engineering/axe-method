# Pattern-Directed Refactorings (Kerievsky Catalog)

Source: Joshua Kerievsky, "Refactoring to Patterns" (2004); Martin Fowler, "Refactoring" (2nd Ed., 2018)

## Creation Patterns

### Replace Constructors with Creation Methods
**Motivation:** Multiple constructors with different signatures are confusing.
**Mechanics:**
1. Find a constructor you'd like to replace
2. Create a creation method that calls the constructor
3. Replace all calls to the constructor with calls to the creation method
4. Repeat for other constructors
5. Optionally make constructors private

**Blast radius:**
- All constructor calls
- Factory usage patterns

---

### Move Creation Knowledge to Factory
**Motivation:** Creation logic is spread across many classes.
**Mechanics:**
1. Create a factory class
2. Move creation method to factory
3. Update callers to use factory
4. Consider making constructor package-private

**Blast radius:**
- All creation sites
- DI/IoC registrations

---

### Encapsulate Classes with Factory
**Motivation:** Clients depend on concrete classes when they should depend on interface.
**Mechanics:**
1. Create an interface from the public methods
2. Make concrete class(es) implement the interface
3. Create a factory that returns the interface type
4. Make concrete class constructors package-private
5. Update all clients to use factory and interface

**Blast radius:**
- Direct instantiation (must change)
- Type declarations (change to interface)
- Imports

---

### Introduce Polymorphic Creation with Factory Method
**Motivation:** Subclasses need to instantiate different objects.
**Mechanics:**
1. Find instantiation in superclass that subclasses need to vary
2. Extract creation into a method
3. Override in subclasses to create different objects
4. Consider making it abstract if no default

**Blast radius:**
- Instantiation to extract
- Subclass overrides needed

---

## Simplification Patterns

### Compose Method
**Motivation:** Method does too much, hard to understand.
**Mechanics:**
1. Make small, well-named methods from code fragments
2. Methods should be at same level of abstraction
3. Minimize parameters (use instance variables)
4. Ensure all operations are at same level of detail

**Blast radius:** Usually contained to single class.

---

### Replace Conditional Logic with Strategy
**Motivation:** Complex conditional controls behavior.
**Mechanics:**
1. Create Strategy interface
2. Create concrete strategy for each branch
3. Create context to hold strategy
4. Replace conditional with strategy call
5. Clients set strategy based on configuration

**Blast radius:**
- The conditional to replace
- Type setting locations
- Test coverage for each branch

---

### Replace Conditional Dispatcher with Command
**Motivation:** Conditional dispatches requests to behaviors.
**Mechanics:**
1. Create Command interface with execute method
2. Create concrete command for each branch
3. Create command map or factory
4. Replace conditional with command lookup and execute

**Blast radius:**
- Dispatch conditional
- Action handlers

---

### Replace State-Altering Conditionals with State
**Motivation:** Object behavior varies based on internal state with complex transitions.
**Mechanics:**
1. Create State interface with all state-varying methods
2. Create concrete state classes
3. State classes handle transitions by setting next state
4. Context delegates to current state

**Blast radius:**
- State checks
- State transitions
- All methods that vary by state

---

### Replace Implicit Tree with Composite
**Motivation:** Implicit tree with special handling for leaves vs branches.
**Mechanics:**
1. Create Component interface with operations
2. Create Leaf implementing Component
3. Create Composite implementing Component with child collection
4. Composite operations iterate over children

**Blast radius:**
- Tree walking code
- Recursive methods

---

### Replace Implicit Language with Interpreter
**Motivation:** Parsing/evaluating a mini-language is spread through code.
**Mechanics:**
1. Create Expression interface with interpret method
2. Create concrete expressions (Terminal and Nonterminal)
3. Build AST from input
4. Interpret by walking AST

**Blast radius:**
- Parsing code
- String operations that might be language handling

---

## Generalization Patterns

### Form Template Method
**Motivation:** Subclasses have methods with similar structure but different details.
**Mechanics:**
1. Identify similar methods in sibling classes
2. Extract differing steps to abstract methods
3. Pull common structure to superclass as template method
4. Subclasses implement the abstract steps

**Blast radius:**
- Similar methods across classes
- Tests for each sibling

---

### Extract Composite
**Motivation:** Subclasses contain duplicate code for handling children.
**Mechanics:**
1. Create Composite superclass
2. Move child handling to Composite
3. Subclasses extend Composite
4. Use Template Method for varying behavior

**Blast radius:**
- Child handling code to extract
- Tests for child behavior

---

### Replace One/Many Distinctions with Composite
**Motivation:** Code has special cases for single vs multiple items.
**Mechanics:**
1. Create Composite that can hold one or many
2. Single item becomes Composite of one
3. Remove conditional code for one/many distinction

**Blast radius:**
- One/many checks
- Multiple item handling

---

### Replace Hard-Coded Notifications with Observer
**Motivation:** Object notifies specific other objects on state change.
**Mechanics:**
1. Create Observer interface
2. Create Subject with registration methods
3. Subject notifies registered observers
4. Replace direct calls with notify

**Blast radius:**
- Direct notification calls
- Listener/callback patterns already present

---

### Unify Interfaces with Adapter
**Motivation:** Clients need similar behavior from classes with different interfaces.
**Mechanics:**
1. Create common interface for desired behavior
2. Create adapter for each class
3. Adapter implements interface, delegates to adapted class
4. Clients use interface

**Blast radius:**
- Classes to unify
- All usages of these classes

---

### Extract Adapter
**Motivation:** One class adapts another but has other responsibilities too.
**Mechanics:**
1. Create Adapter class with adaptation logic
2. Move adaptation code from original class
3. Original class uses adapter
4. Other clients can also use adapter

**Blast radius:**
- Adaptation code in the class
- Tests for adaptation behavior

---

## Protection Patterns

### Replace Type Code with Class
**Motivation:** Type code is just an integer/string with magic values.
**Mechanics:**
1. Create class for the type code
2. Create static instances for each value
3. Replace type code field with new type
4. Replace comparisons with equals or identity

**Blast radius:**
- Magic value comparisons
- Type code assignments
- Type code declarations

---

### Limit Instantiation with Singleton
**Motivation:** Only one instance should exist.
**Mechanics:**
1. Make constructor private
2. Create static instance holder
3. Create static getInstance method
4. Replace all `new` calls with getInstance

**Blast radius:**
- All instantiation sites
- Any subclasses (Singleton usually prevents these)

**Note:** Singleton generally conflicts with IoC/DI principles. Prefer dependency injection for managing shared instances — create one instance in the Composition Root and inject it where needed. This achieves the same "single instance" goal while keeping types testable.

---

### Introduce Null Object
**Motivation:** Repeated null checks clutter code.
**Mechanics:**
1. Create interface from class
2. Create Null Object class implementing interface
3. Null Object returns safe defaults
4. Replace null returns with Null Object
5. Remove null checks in clients

**Blast radius:**
- Null checks for this type
- Methods returning null
- Optional/Maybe wrapping

---

## Accumulation Patterns

### Move Accumulation to Collecting Parameter
**Motivation:** Method builds result by modifying accumulator passed around.
**Mechanics:**
1. Add collecting parameter to method signature
2. Pass accumulator through call chain
3. Each method adds to accumulator
4. Remove return value (accumulator has result)

**Blast radius:**
- Methods that return accumulated value
- Call sites (need to pass accumulator)

---

### Move Accumulation to Visitor
**Motivation:** Complex accumulation over heterogeneous structure.
**Mechanics:**
1. Create Visitor interface with visit method per type
2. Add accept method to each element type
3. Visitor accumulates as it visits
4. Client creates visitor, walks structure

**Blast radius:**
- Accumulation over structure
- Type checks in accumulation

## Sources

- Joshua Kerievsky, "Refactoring to Patterns" (2004)
- Martin Fowler, "Refactoring: Improving the Design of Existing Code" (2nd Ed., 2018)
