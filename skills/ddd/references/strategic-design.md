# DDD Strategic Design Reference

Sources: Eric Evans, "Domain-Driven Design" (2003); Vaughn Vernon, "Implementing Domain-Driven Design" (2013); Eric Evans, "Domain-Driven Design Reference" (2015)

## Ubiquitous Language

The vocabulary of the domain model must be spoken by everyone — developers, domain experts, documentation, code, and conversations.

The language includes:
- Names of classes and prominent operations
- Terms for rules made explicit in the model
- High-level organizing principles from the model

Why it matters:
- If the model isn't connected to implementation, it has no meaning
- If the language isn't shared, knowledge crunching falls apart
- Gaps in the language reveal gaps in the model

In practice:
- When domain experts use this language, they quickly discover where the model is inadequate
- Changes to the language ARE changes to the model
- The model-based language describes tasks, functionality, and artifacts — not just data

## Model-Driven Design

The model and the implementation must be intimately connected. The code IS an expression of the model.

Tightly relating the code to the model:
- Gives the code meaning
- Makes the model relevant
- Allows developers and domain experts to share a language

The imperative: When a model doesn't seem practical for implementation, search for a new model. When a model doesn't faithfully express domain concepts, search for a new model.

## Knowledge Crunching

Effective domain modelers are knowledge crunchers. The process:

1. Organize one idea after another
2. Search for the simple view that makes sense of the mass
3. Continuously refine with domain experts feeding into it

Knowledge crunching is NOT solitary. The interaction between team members changes as all members crunch the model together.

## Bounded Context

A model is not universal — it applies within a CONTEXT.

A Bounded Context:
- Defines the boundary within which a particular model is valid
- Has a name that is part of the Ubiquitous Language
- Contains a complete model that is self-consistent

Why this matters:
- Different teams may have different models
- Different parts of a system may need different models
- One model cannot serve all needs without becoming convoluted

## Context Map

When you have multiple Bounded Contexts, you need to understand how they relate.

Context relationships:

| Relationship | Description |
|-------------|-------------|
| **Shared Kernel** | Two teams share a subset of the model |
| **Customer/Supplier** | Downstream team depends on upstream; upstream accommodates |
| **Conformist** | Downstream conforms to upstream model (no negotiation) |
| **Anticorruption Layer** | Downstream creates a translation layer to protect its model |
| **Separate Ways** | Teams go their own way (no integration) |
| **Open Host Service** | A protocol that gives access to your system |
| **Published Language** | A well-documented shared language for interchange between contexts |

The map: Draw the relationships between contexts. Make integration points explicit.

## Distillation

Not all parts of a domain model are equal. Identify and isolate the Core Domain.

**Core Domain:**
- The most fundamental, differentiating concept in the model
- Why you are building this system instead of buying it
- Where you invest your best people

**Generic Subdomains:**
- Necessary but not distinctive
- Consider off-the-shelf solutions or separate teams

The discipline: Boil the model down. Find the Core Domain and make it shine.

## Large-Scale Structure

For very large systems, impose an overarching structure:

- **Evolving Order** — Let the structure emerge through refactoring
- **System Metaphor** — A story that unifies and explains the system
- **Responsibility Layers** — Divide by broad categories of responsibility
- **Knowledge Level** — Separate what users can customize from base behavior

## Breakthrough

Projects do get stuck. Models that seem adequate can suddenly break. The key is to be willing to explore new models when the current one struggles. Knowledge crunching is an exploration — you can't know where you will end up.

## Sources

- Eric Evans, "Domain-Driven Design: Tackling Complexity in the Heart of Software" (2003)
- Vaughn Vernon, "Implementing Domain-Driven Design" (2013)
- Eric Evans, "Domain-Driven Design Reference" (2015)
