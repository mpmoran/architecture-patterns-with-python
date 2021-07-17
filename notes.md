# Notes from Architecture Patterns with Python

## Preface

- Best way of structuring an application
  - Easy to test
  - "Core business logic is covered by unit tests"
  - Minimize integration and end-to-end tests
- Purpose of book is to teach how to "build distributed systems that model real-world business problems". Tech choices should become minor implementation details
- Python is chosen because scripts are becoming enterprise software.
- Three tools for managing complexity
  - TDD: helps with building correct code that is easy to refactor/add new features without fear of regression
  - DDD: focus on building a good model of business domain
  - Reactive microservices: loosely coupled services integrated by messages
- Preview
  - Ch 1 and 7
    - Domain modeling and DDD.
    - Models with no external dependencies and fast unit tests
    - Choosing the right aggregate and its effect on data integrity
  - Ch 2, 4, and 5: Three patterns that help with keeping model free of external dependencies
  - Ch 3 and 6: How to choose abstractions and the effect on coupling.
  - Ch 8-11: Three patterns:
    1. Domain events
    1. Message bus
    1. Handler
  - Ch 12: Command-query responsibility segregation
  - Ch 13:
    - Clean up dependencies
    - Implement simple dependency injection framework

## Introduction

- Designs can easily go wrong, resulting in "confusing morass of manager classes and util modules", called the **Big Ball of Mud** anti-pattern. Business logic is spread throughout layers of application.
- **Encapsulation**: Simplifying behavior and hiding data
- **Abstraction**: A well-defined object or function that performs a given task
- Both encapsulation and abstraction result in more expressive, more testable, and more maintainable code.
- **Responsibility-driven design**: think about code in terms of behavior rather than data or algorithms
- **Dependency**: when one function, module, or object knows about or needs another
- **Layered architecture**: code that is divided into categories or roles with "rules about which categories of code can call each other".
  - **Three-layered architecture**: Presentation layer -> Business logic/Domain model -> Database layer
- **Dependency inversion principle (DIP)**:
  1. "High-level modules should not depend on low-level modules. Both should depend on abstractions".
      - High-level modules are the code that matters to an organization. The functions, classes, and packages that deal with real-world concepts. These shouldn't depend on technical details but should use abstractions.
      - Low-level modules are code that organization does not care about, e.g., file systems, network sockets, HTTP, etc.
  1. Details should depend on abstractions, not the other way around.

## Part I. Building an Architecture to Support Domain Modeling

- Don't start design with building a database schema. Start with the object model. Behavior should come first and drive storage requirements.
- Part I is about how to build an object model through TDD that is decoupled from technical concerns.

### Ch. 1 Domain Modeling

- Purpose of chapter: How to model business processes with code that is compatible with TDD. Introduction of key patterns for modeling: entity, value object, and domain service.
- **Domain**: the problem to be solved
  - e.g., how to navigate home if abducted by aliens
- **Model**: "a map of a process or phenomenon that captures a useful property"
  - e.g., predicting the movement of a thrown ball
- Domain of the book: how to treat goods in transit as if they are part of stock
- First steps
  1. Meet with business experts to understand domain
  1. Agree on a glossary of terms
  1. Agree on rules for first minimal version of domain model
      - Rules should be expressed in business jargon (**ubiquitous language**).
- Names in code (variables, functions, classes) should be the same as the language of domain experts.

#### Value Object Pattern

- **Value object**: a domain object uniquely identified by the data it holds; should probably be immutable
  - Changing a value of the object results in a new object
    - e.g., A name is a value object. When it changes, it is something different.
  - Use when a business concept has data but no identity, e.g., no unique identifier property like a reference/order number

#### Entity

- **entity**: a domain object that has a long-lived identity
  - Entities have identity equality. Their values can change but they are still the same thing.
    - e.g., People are entities. They can change their name but they are still the same person.

#### Domain Service Function

- Not everything has to be an object. "Sometimes, it just isn't a thing".
- These don't have a home in an entity or value object
  - e.g., allocating an order among different batches of stock

#### Exceptions as Domain Concept

- **Domain exception**: an exception that represents a domain concept
  - e.g., an out-of-stock exception

### Ch. 2 Repository Pattern

- **Repository pattern**: an abstraction over data storage so that the model layer is decoupled from the data layer
  - It pretends that all data is stored in memory, preventing the coupling of the domain model to the database.
  - The simplest repository has add() and get() methods.
    - These methods are to be used in the domain and service layers.
  - Trade offs
    - The repository pattern has a cost of added complexity, more moving parts, and additional maintenance.
    - Creating a fake repo, for testing purposes, should be easy; otherwise, the design/abstraction is too complicated.
    - Simple applications don't need a repository pattern. Coupling is OK.
- The domain model should have no stateful dependencies. Infrastructure concerns should not "bleed over" into the domain model.
- **Onion architecture**: The model is on the "inside" and dependencies flow inward to it.
- **Object-relational mapper (ORM)**: a framework that bridges the gap between objects/domain models and databases
  - Gives us **persistence ignorance**: the domain model doesn't need to know how data is loaded or persisted
  - We map the domain model to the database model in the persistence storage implementation, not in the domain model implementation.
- **Port**: interface between application and what we are abstracting away
- **Adapter**: implementation behind the interface/port
