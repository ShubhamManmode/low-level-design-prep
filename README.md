Low-Level Design (LLD)

This repository contains my Low-Level Design (LLD) learning and implementation journey, focused on building scalable, maintainable, extensible, and testable object-oriented systems.

The goal is to understand how to translate requirements into classes, interfaces, relationships, responsibilities, and design patterns and implement them using clean code principles.

⸻

🎯 Goals

* Master Object-Oriented Design
* Understand SOLID principles deeply
* Learn how to identify classes and responsibilities
* Understand relationships between objects
* Learn UML and class diagrams
* Master commonly used Design Patterns
* Practice designing real-world systems
* Write extensible and maintainable code
* Understand concurrency in LLD
* Prepare for LLD / Machine Coding interviews
* Learn how to evolve a simple design into a production-ready design

⸻

📚 LLD Syllabus

1. Object-Oriented Programming

Core OOP Concepts

* Class and Object
* Encapsulation
* Abstraction
* Inheritance
* Polymorphism
* Composition
* Association
* Aggregation

Advanced OOP

* Abstract Class
* Interface
* Virtual / Override
* Method Overloading
* Method Overriding
* Dependency Injection
* Composition over Inheritance
* Favoring interfaces over concrete implementations

⸻

2. Object Relationships

Understand how objects interact with each other.

* Association
* Aggregation
* Composition
* Dependency
* Inheritance
* “is-a” relationship
* “has-a” relationship
* “uses-a” relationship

Example

Car
 └── Engine
Car HAS-A Engine

⸻

3. SOLID Principles

S — Single Responsibility Principle

* One class should have one reason to change
* Identify responsibilities
* Separate business responsibilities

O — Open/Closed Principle

* Open for extension
* Closed for modification
* Strategy-based extension

L — Liskov Substitution Principle

* Substitutability
* Proper inheritance
* Avoiding incorrect inheritance hierarchies

I — Interface Segregation Principle

* Small interfaces
* Role-based interfaces
* Avoid fat interfaces

D — Dependency Inversion Principle

* Depend on abstractions
* Dependency Injection
* High-level vs low-level modules

⸻

4. Design Principles

* DRY — Don’t Repeat Yourself
* KISS — Keep It Simple
* YAGNI — You Aren’t Gonna Need It
* Separation of Concerns
* Encapsulation
* Information Hiding
* Law of Demeter
* Composition over Inheritance
* Program to an interface
* High cohesion
* Low coupling

⸻

5. UML & Design Diagrams

Learn to represent designs visually before implementing them.

UML Diagrams

* Class Diagram
* Sequence Diagram
* Activity Diagram
* State Diagram
* Use Case Diagram

Class Diagram Concepts

* Class
* Interface
* Abstract Class
* Attributes
* Methods
* Visibility
* Relationships
* Multiplicity

⸻

6. Design Patterns

Design patterns are reusable solutions to commonly occurring design problems.

⸻

6.1 Creational Patterns

Singleton

* Singleton implementation
* Thread-safe Singleton
* Lazy Singleton
* Problems with Singleton
* When to use / avoid Singleton

Factory

* Simple Factory
* Factory Method
* Abstract Factory

Builder

* Builder Pattern
* Fluent Builder
* Handling complex object construction

Prototype

* Prototype Pattern
* Shallow copy
* Deep copy

⸻

6.2 Structural Patterns

Adapter

* Adapter Pattern
* Interface compatibility
* Legacy system integration

Decorator

* Dynamic behavior extension
* Decorator vs inheritance

Facade

* Simplifying complex subsystems
* API abstraction

Proxy

* Virtual Proxy
* Protection Proxy
* Remote Proxy
* Caching Proxy

Composite

* Tree structures
* Part-whole relationships

Bridge

* Separating abstraction from implementation

Flyweight

* Object sharing
* Memory optimization

⸻

6.3 Behavioral Patterns

Strategy

* Encapsulating algorithms
* Runtime strategy selection
* Strategy vs if/else

Observer

* One-to-many dependency
* Event-driven design
* Publisher / Subscriber

Chain of Responsibility

* Request pipeline
* Handler chain
* Middleware-like architecture

Command

* Encapsulating requests
* Undo / redo
* Queueing commands

State

* State-dependent behavior
* State transitions

Template Method

* Common algorithm structure
* Customizable steps

Iterator

* Traversing collections
* Custom iterators

Mediator

* Centralized communication
* Reducing object coupling

Memento

* Saving object state
* Undo functionality

Visitor

* Adding operations without modifying object structures

⸻

7. Pattern Selection

Learn to identify which pattern to use and why.

Problem	Possible Pattern
Object creation is complex	Builder
Object creation varies	Factory
Multiple related object families	Abstract Factory
Need interchangeable algorithms	Strategy
Need notifications	Observer
Add behavior dynamically	Decorator
Adapt incompatible interfaces	Adapter
Simplify complex subsystem	Facade
Control access to an object	Proxy
Request should be represented as object	Command
Object behavior changes by state	State
Chain of handlers	Chain of Responsibility
Tree structure	Composite
Shared objects to reduce memory	Flyweight

⸻

8. LLD Problem-Solving Framework

For every LLD problem, follow this process:

Step 1 — Understand Requirements

* Functional requirements
* Non-functional requirements
* Actors
* Main use cases
* Constraints
* Edge cases

Step 2 — Identify Core Entities

Ask:

What are the nouns in the problem?

Example:

Parking Lot
ParkingLot
Floor
ParkingSpot
Vehicle
Ticket
Payment

Step 3 — Identify Responsibilities

Ask:

What should each class be responsible for?

Step 4 — Define Relationships

Determine:

* HAS-A
* IS-A
* USES-A

Step 5 — Define Interfaces

Identify behavior that can have multiple implementations.

Step 6 — Apply SOLID

Check:

* SRP
* OCP
* LSP
* ISP
* DIP

Step 7 — Identify Design Patterns

Ask:

Is there a recurring design problem here?

Step 8 — Create Class Diagram

Represent:

Classes
Interfaces
Relationships
Methods
Attributes

Step 9 — Implement

Write clean, extensible code.

Step 10 — Test

Cover:

* Happy path
* Edge cases
* Invalid inputs
* Multiple implementations
* Concurrency where applicable

⸻

9. Important LLD Case Studies

Beginner

* Parking Lot
* Tic Tac Toe
* Snake and Ladder
* Car Rental System
* Library Management System
* ATM
* Vending Machine
* Coffee Machine
* Elevator System

Intermediate

* Splitwise
* Chess
* Deck of Cards
* BookMyShow
* Movie Ticket Booking
* Cab Booking
* Food Delivery System
* Hotel Booking
* Inventory Management System
* Meeting Scheduler

Advanced

* Logger
* Rate Limiter
* Cache
* In-Memory Database
* Task Scheduler
* Notification System
* File System
* Distributed Lock
* Pub/Sub System
* Message Queue
* Workflow Engine
* Payment System

⸻

10. Concurrency in LLD

Understand how multi-threaded systems affect object design.

Topics

* Thread safety
* Race conditions
* Critical section
* Lock / Mutex
* Semaphore
* Monitor
* Deadlock
* Starvation
* Atomic operations
* Immutable objects
* Concurrent collections
* Thread-safe Singleton
* Producer / Consumer
* Read/Write locks

C# Specific

* lock
* Monitor
* SemaphoreSlim
* Mutex
* Interlocked
* ConcurrentDictionary
* async / await
* CancellationToken

⸻

11. Clean Code

* Meaningful names
* Small methods
* Small classes
* Avoid magic numbers
* Avoid unnecessary comments
* Avoid deep nesting
* Proper exception handling
* Immutability
* Dependency Injection
* Testable code
* Avoid premature abstraction

⸻

12. Code Quality

For every implementation, evaluate:

Coupling

How dependent are classes on each other?

High Coupling ❌
Low Coupling  ✅

Cohesion

Does a class contain closely related responsibilities?

High Cohesion ✅
Low Cohesion  ❌

Extensibility

Can we add new functionality without modifying existing code?

Testability

Can individual components be tested independently?

Maintainability

Can another developer easily understand and modify the code?

⸻

13. Refactoring

Learn how to identify and fix bad designs.

Code Smells

* God Class
* Long Method
* Large Class
* Duplicate Code
* Tight Coupling
* Shotgun Surgery
* Feature Envy
* Primitive Obsession
* Deep Inheritance
* Too many if/else statements

Refactoring Techniques

* Extract Class
* Extract Method
* Replace Conditional with Polymorphism
* Introduce Interface
* Dependency Injection
* Composition over Inheritance

⸻

14. LLD Interview Preparation

For each problem, practice explaining:

1. Requirements
2. Assumptions
3. Entities
4. Responsibilities
5. Class design
6. Interfaces
7. Relationships
8. Design patterns
9. SOLID principles
10. Edge cases
11. Concurrency
12. Extensibility
13. Testing
14. Trade-offs

⸻

15. Machine Coding

Practice implementing complete systems within a limited time.

Focus Areas

* Requirement clarification
* Class design
* Interface design
* SOLID
* Design patterns
* Clean code
* Exception handling
* Validation
* Thread safety
* Unit testing
* Extensibility

Target

Build a working implementation instead of only drawing a class diagram.

⸻

16. LLD vs HLD

LLD	HLD
Classes	Services
Interfaces	APIs
Objects	Databases
Methods	Caches
Design Patterns	Message Queues
SOLID	Load Balancers
Class Diagram	Architecture Diagram
Object interaction	Service interaction
Code-level design	System-level design

⸻

🧠 LLD Mental Model

When you receive an LLD problem, think:

Requirements
     ↓
Use Cases
     ↓
Entities
     ↓
Responsibilities
     ↓
Relationships
     ↓
Interfaces
     ↓
SOLID
     ↓
Design Patterns
     ↓
Class Diagram
     ↓
Implementation
     ↓
Testing
     ↓
Concurrency + Extensibility

⸻

📂 Suggested Repository Structure

low-level-design/
│
├── README.md
│
├── 01-oops/
│   ├── encapsulation/
│   ├── abstraction/
│   ├── inheritance/
│   ├── polymorphism/
│   └── composition/
│
├── 02-solid/
│   ├── srp/
│   ├── ocp/
│   ├── lsp/
│   ├── isp/
│   └── dip/
│
├── 03-design-patterns/
│   ├── creational/
│   ├── structural/
│   └── behavioral/
│
├── 04-uml/
│   ├── class-diagrams/
│   ├── sequence-diagrams/
│   └── state-diagrams/
│
├── 05-case-studies/
│   ├── parking-lot/
│   ├── tic-tac-toe/
│   ├── elevator/
│   ├── vending-machine/
│   ├── splitwise/
│   ├── chess/
│   └── logger/
│
├── 06-concurrency/
│
├── 07-machine-coding/
│
└── 08-interview-questions/

⸻

🚀 Definition of Done

I consider my LLD preparation complete when I can:

* Convert requirements into objects
* Assign responsibilities correctly
* Design interfaces
* Explain object relationships
* Apply SOLID naturally
* Identify appropriate design patterns
* Draw class and sequence diagrams
* Write clean production-quality code
* Refactor bad designs
* Handle concurrency
* Design extensible systems
* Explain design trade-offs
* Solve an LLD problem without memorizing a solution

⸻

🎯 Final Objective

The objective of this repository is not to memorize design patterns.

The objective is to develop the ability to look at a problem and think:

What are the responsibilities?
What objects should exist?
How should they communicate?
What can change in the future?
How can I keep the design loosely coupled and highly cohesive?

Once this thinking becomes natural, Design Patterns become tools rather than things to memorize.