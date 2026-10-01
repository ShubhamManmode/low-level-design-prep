# abstraction

Abstraction is the process of showing only the essential details to the outside world and hiding the internal implementation complexity.

In simple words, abstraction means:
- focus on what an object does,
- not how it does it.

Example:
- A driver knows how to use a car by pressing accelerator, brake, and steering wheel.
- He does not need to know the internal combustion details, engine timing, fuel injection, or gearbox mechanics.

That is abstraction.

## Real-world meaning
Imagine a `TV remote`:
- You press a button to switch channels.
- You do not care how the remote internally sends signals to the TV.
- The remote exposes a simple interface, while the internal logic remains hidden.

This is abstraction at work.

## In OOP
Abstraction is one of the core principles of Object-Oriented Programming.
It allows us to:
- reduce complexity,
- provide a clean interface,
- hide unnecessary details,
- make code easier to maintain and extend.

## Core idea
Abstraction is about:
- exposing only relevant behavior,
- hiding internal implementation,
- using a stable contract.

Example:
```java
class BankAccount {
    public void deposit(double amount) {
        // internal logic
    }

    public void withdraw(double amount) {
        // internal logic
    }
}
```

The user knows that calling `deposit()` adds money.
The user does not need to know how the balance is stored or validated.

## Why abstraction is important
Abstraction helps in:
- reducing cognitive load,
- improving code readability,
- decoupling system components,
- making code reusable,
- allowing changes without affecting users,
- simplifying low-level design and architecture.

## Abstraction in daily life
### Case 1: Mobile phone
A user only knows:
- call,
- message,
- camera,
- internet.

The actual chip design, signal processing, battery management, and OS internals are hidden.

### Case 2: ATM machine
You insert a card and enter a PIN.
You do not know how authentication, bank verification, or cash dispensing work internally.

### Case 3: Car dashboard
You see fuel, speed, and warnings.
You do not care about the internal engine control units and sensors.

## Abstraction in programming: different forms

### 1. Abstract class
An abstract class can contain:
- abstract methods (without implementation),
- concrete methods (with implementation),
- fields.

It is used when multiple related classes share common logic.

```java
abstract class Animal {
    public abstract void sound();

    public void sleep() {
        System.out.println("Sleeping...");
    }
}

class Dog extends Animal {
    public void sound() {
        System.out.println("Bark");
    }
}

class Cat extends Animal {
    public void sound() {
        System.out.println("Meow");
    }
}
```

Here, `Animal` provides the abstraction:
- every animal makes a sound,
- but each animal implements it differently.

### 2. Interface
An interface defines a contract.
It only declares methods; the implementing class provides the actual implementation.

```java
interface PaymentGateway {
    void pay(double amount);
}

class UpiPayment implements PaymentGateway {
    public void pay(double amount) {
        System.out.println("Paying " + amount + " via UPI");
    }
}

class CardPayment implements PaymentGateway {
    public void pay(double amount) {
        System.out.println("Paying " + amount + " via Card");
    }
}
```

Now the system can call `pay()` without caring which payment method is used.

This is a classic abstraction example.

## Abstraction vs Encapsulation
These two are related but different.

### Abstraction
- focus on what something does,
- hides implementation details,
- defines a high-level view.

### Encapsulation
- focuses on bundling data and methods together,
- protects internal state,
- restricts direct access.

Example:
```java
class Account {
    private double balance;

    public double getBalance() {
        return balance;
    }

    public void deposit(double amount) {
        balance += amount;
    }
}
```

- `balance` is encapsulated using private access.
- `deposit()` gives an abstract operation for users.

So:
- Abstraction = hiding complexity
- Encapsulation = hiding data and controls

## Abstraction vs Inheritance
Inheritance is about code reuse and hierarchy.
Abstraction is about defining a common contract or behavior.

- Inheritance creates a class relationship.
- Abstraction defines what must be done, not how.

Example:
```java
abstract class Vehicle {
    abstract void start();
}

class Bike extends Vehicle {
    public void start() {
        System.out.println("Bike started");
    }
}
```

The `Vehicle` concept is abstract.
`Bike` inherits that abstract behavior and implements it.

## Abstraction with real-life design examples

### Case 1: Payment system
```java
interface PaymentMethod {
    void processPayment(double amount);
}
```

Different payment types can implement this:
- UPI
- Credit Card
- Net Banking
- Wallet

The order service only calls `processPayment()`. It does not know which payment provider is used internally.

### Case 2: Database access
```java
interface UserRepository {
    void saveUser(User user);
    User findById(int id);
}
```

The business layer uses repository methods, without knowing if the data is stored in MySQL, MongoDB, or Redis.

### Case 3: Ride-sharing app
A rider only sees:
- book ride,
- track driver,
- pay fare.

The internal logic for route calculation, driver matching, and pricing remains hidden.

## Abstraction in low-level design (LLD)
Abstraction is extremely important in LLD because systems are composed of many moving parts.

Typical LLD abstraction patterns:
- expose service interfaces,
- hide database logic behind repositories,
- separate use cases from implementation,
- define APIs instead of concrete classes,
- depend on interfaces instead of concrete classes.

Example:
```java
interface NotificationService {
    void send(String message);
}

class EmailService implements NotificationService {
    public void send(String message) {
        System.out.println("Email sent: " + message);
    }
}

class SMSService implements NotificationService {
    public void send(String message) {
        System.out.println("SMS sent: " + message);
    }
}
```

The order service can simply do:
```java
class OrderService {
    private final NotificationService notificationService;

    public OrderService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }

    public void placeOrder() {
        notificationService.send("Order placed successfully");
    }
}
```

Here, the `OrderService` depends on the abstraction `NotificationService`, not on a concrete email or SMS implementation.

This is one of the most important abstraction patterns in LLD.

## When should you use abstraction?
Use abstraction when:
- several classes share common behavior,
- implementation details are not needed by the caller,
- you want to hide complexity,
- you want to support multiple implementations,
- you want a stable API even if internals change,
- you want to build scalable and maintainable systems.

## When abstraction is not enough
If a class does not have a clear common behavior, adding abstraction too early can make code:
- complicated,
- confusing,
- over-engineered,
- harder to understand.

So abstraction should be used when there is a real need for a generalized design.

## Common interview “all cases” summary

### Case 1: Same action, different implementation
```java
interface Shape {
    double area();
}
```
Circle and Rectangle implement it differently.

### Case 2: Shared behavior with reusable code
```java
abstract class Employee {
    abstract double salary();
    void printDetails() {
        System.out.println("Employee details");
    }
}
```

### Case 3: API contract
```java
interface Cache {
    void put(String key, String value);
    String get(String key);
}
```

The application does not care about Redis, Caffeine, or Memcached internals.

### Case 4: External system integration
```java
interface PaymentProvider {
    boolean validate(String token);
}
```

Different providers can be plugged in without changing the client code.

## Short definition for interviews
Abstraction is the concept of hiding internal complexity and exposing only the necessary behavior to the user.

## Cross questions and answers

### Q1. What is abstraction?
Answer: It is the process of hiding implementation details and showing only essential features.

### Q2. What is the difference between abstraction and encapsulation?
Answer: Abstraction focuses on hiding complexity and showing only relevant behavior. Encapsulation focuses on protecting data and bundling it with methods.

### Q3. Can abstraction exist without inheritance?
Answer: Yes. Abstraction can be implemented using interfaces and abstract classes, without requiring inheritance in all scenarios.

### Q4. Why is abstraction useful in system design?
Answer: It reduces dependency on implementation details and allows easy substitution of components.

### Q5. Abstract class vs interface: which is better?
Answer:
- Use abstract class when you want shared logic.
- Use interface when you want a contract or multiple inheritance-like behavior.

### Q6. Is abstraction same as data hiding?
Answer: Not exactly. Data hiding is part of encapsulation. Abstraction is a broader concept of simplifying a system.

### Q7. Why do we use interfaces in LLD?
Answer: To define contracts, reduce coupling, and enable easy swapping of implementations.

### Q8. What is the benefit of using `PaymentGateway` interface in an application?
Answer: The business logic does not change even if the payment provider changes from UPI to Card or Wallet.

### Q9. Can you have abstraction at method level?
Answer: Yes. A method can abstract the internal process while exposing a simple operation. Example: `startEngine()` hides actual engine startup logic.

### Q10. How is abstraction used in real life?
Answer: Phone, car dashboard, remote control, ATM machine, and online payment systems all hide internal complexity and expose a simple interface.

## One-line final answer
Abstraction means exposing only the necessary details and hiding the complex internals so that a system remains simple, reusable, and maintainable.

## Quick revision notes
- Abstraction = hide complexity
- Encapsulation = hide data and control access
- Interface/abstract class = tools for abstraction
- In LLD, abstraction helps in clean architecture and loose coupling

## Interview answer template
> Abstraction is a design principle where we expose only the essential behavior and hide the internal implementation from the user. It helps reduce complexity, improve maintainability, and allow code to evolve without affecting callers. In Java, abstraction is achieved through abstract classes and interfaces. In low-level design, it is used heavily through service interfaces, repository abstractions, and strategy patterns.

## Final thought
If you understand abstraction properly, you can design cleaner systems where classes talk to contracts instead of concrete implementations. That is one of the biggest skills needed for good object-oriented and low-level design interviews.
