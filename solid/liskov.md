# Liskov Substitution Principle (LSP)

## What is LSP?

The Liskov Substitution Principle states:

> If a program is using a base class, it should be able to use any subclass instance without knowing it is a subclass, and the program should continue to work correctly.

In simpler words:

- A subclass must not weaken the behavior promised by the parent class.
- It must preserve the contract of the parent.
- It should be safe to replace a parent object with a child object without breaking the system.

This principle is one of the five SOLID principles and is especially important in object-oriented design and polymorphism.

---

## Why does it matter?

When LSP is violated, the code may still compile, but it fails at runtime because a subtype behaves in an unexpected way.

This creates problems like:

- hidden assumptions in calling code
- unexpected exceptions
- invalid state transitions
- brittle, hard-to-maintain inheritance hierarchies

The root issue is usually this: the subclass is not truly a valid replacement for the base type.

---

## The core idea: behavioral compatibility

A subclass should not:

- remove or narrow inherited behavior
- throw exceptions for operations that the parent guarantees to work
- change the meaning of method contracts
- strengthen preconditions or weaken postconditions in a dangerous way

The parent defines a contract. The child should uphold that contract, not alter it unpredictably.

---

## Example of LSP violation

### Bad design

```java
class Bird {
    public void fly() {
        System.out.println("Flying");
    }
}

class Penguin extends Bird {
    @Override
    public void fly() {
        throw new UnsupportedOperationException("Penguins cannot fly");
    }
}
```

This looks like inheritance, but it breaks the contract of `Bird`.

If the application does this:

```java
void makeBirdFly(Bird bird) {
    bird.fly();
}
```

then passing a `Penguin` breaks the expectation that every `Bird` can fly.

### Why this violates LSP

The subclass changes the behavior of a method in a way that contradicts the parent type's promise.

`Bird` implies “all birds can fly,” but `Penguin` is a bird that cannot.

---

## Correct way to model it

The solution is not to force `Penguin` into a wrong hierarchy. Instead, separate responsibilities.

```java
abstract class Bird {
    public abstract void eat();
}

abstract class FlyingBird extends Bird {
    public abstract void fly();
}

class Sparrow extends FlyingBird {
    @Override
    public void eat() {
        System.out.println("Eating seeds");
    }

    @Override
    public void fly() {
        System.out.println("Sparrow flying");
    }
}

class Penguin extends Bird {
    @Override
    public void eat() {
        System.out.println("Eating fish");
    }
}
```

Now each class matches its correct contract.

The code that flies only accepts `FlyingBird`, not all `Bird`.

---

## Classic Rectangle-Square example

This is one of the most famous LSP examples.

```java
class Rectangle {
    protected int width;
    protected int height;

    public void setWidth(int width) {
        this.width = width;
    }

    public void setHeight(int height) {
        this.height = height;
    }

    public int area() {
        return width * height;
    }
}

class Square extends Rectangle {
    @Override
    public void setWidth(int width) {
        this.width = width;
        this.height = width;
    }

    @Override
    public void setHeight(int height) {
        this.width = height;
        this.height = height;
    }
}
```

This violates LSP because `Square` changes the semantics of `Rectangle`.

A rectangle allows width and height to vary independently, but a square forces them to be equal. If client code expects a rectangle to behave like a normal rectangle, the square breaks that expectation.

---

## The contract perspective

A strong way to think about LSP is through contracts.

For a method, the parent defines:

- preconditions (what must be true before calling the method)
- postconditions (what must be true after the method runs)
- invariants (rules that stay true over time)

A subclass may not:

- require more than the parent required
- guarantee less than the parent guaranteed

This is often summarized as:

- stronger preconditions are bad for subclassing
- weaker postconditions are bad for subclassing

If a subclass makes the method stricter or behaves differently than promised, then it violates LSP.

---

## Real-world analogy: payment methods

Imagine a base class `PaymentProvider`:

```java
abstract class PaymentProvider {
    public abstract void processPayment(double amount);
}
```

Now suppose `WalletPayment` extends it and does this:

```java
class WalletPayment extends PaymentProvider {
    @Override
    public void processPayment(double amount) {
        if (amount > 1000) {
            throw new IllegalArgumentException("Wallet payments are limited to 1000");
        }
        System.out.println("Processed wallet payment");
    }
}
```

This is not a valid subtype if the base class promised to handle any valid payment amount. The subclass is adding a constraint that the parent did not promise.

A better design is to define a more specific abstraction or validate at the right boundary, not silently weaken the contract.

---

## How to detect LSP violations

Look for these warning signs:

- subclass throws exceptions that the parent does not
- subclass ignores base behavior or changes it unexpectedly
- subclass has methods that look inherited but don’t make sense in context
- there are `if (obj instanceof ...)` checks to work around subclass-specific behavior
- base type is used polymorphically, but child types require special handling

If a subclass always needs special case code outside the inheritance hierarchy, the design may be wrong.

---

## LSP and design quality

LSP is not just a theoretical principle. It improves the design of real systems by:

- making inheritance meaningful
- improving polymorphism reliability
- reducing runtime surprises
- enabling safer refactoring
- clarifying domain modeling

A good hierarchy is not just “A is-a B.” It is “A can replace B without breaking the behavior expected by B.”

---

## Quick rule of thumb

Before creating a subclass, ask:

1. Can I replace the parent object with this subclass anywhere the parent is used?
2. Will the calling code still behave correctly?
3. Does the subclass preserve the parent contract?
4. Is the subclass truly a subtype, or just a convenience implementation detail?

If the answer is “no,” the inheritance relationship is likely wrong.

---

## Interview-style Q&A

### Q1: What is the Liskov Substitution Principle in one sentence?

A: Subclasses should be substitutable for their base classes without altering the correctness of the program.

---

### Q2: Why is LSP important in OOP?

A: It ensures polymorphism is safe and reliable. Without it, a subclass may break the assumptions of code written against the parent class.

---

### Q3: What is a common LSP violation?

A: A subclass overriding a method and throwing exceptions that the base method never threw, or changing the method meaning unexpectedly.

Example: `Bird.fly()` being overridden by `Penguin.fly()` to throw an exception.

---

### Q4: How is LSP different from the Open/Closed Principle?

A: OCP says classes should be open for extension but closed for modification. LSP ensures that the extension is actually safe and compatible with the existing base type.

In other words:

- OCP is about extensibility
- LSP is about behavioral compatibility

---

### Q5: What does “contract” mean in LSP?

A: The contract is the set of expectations defined by the base class: method signatures, behavior, invariants, and guarantees.

A subclass must honor that contract, not break it.

---

### Q6: Why is the Rectangle/Square example often used?

A: It clearly shows how a subclass can inherit from a base type but fundamentally violate the assumptions that clients have about the base type.

The problem is not inheritance itself, but the invalid modeling of the relationship.

---

### Q7: How do you fix a design that violates LSP?

A: Revisit the inheritance model. Often the fix is to:

- create a more accurate base abstraction
- split responsibilities into different hierarchies
- use composition instead of inheritance
- avoid inheritance when the subtype is not truly substitutable

---

### Q8: Can composition help avoid LSP violations?

A: Yes. Sometimes inheritance creates a misleading “is-a” relationship. Composition may better model real relationships, especially when behavior differs significantly.

Example: a `Duck` class could contain a `FlyBehavior` object rather than inheriting from a `Bird` hierarchy with rigid assumptions.

---

### Q9: What is the relationship between LSP and testing?

A: LSP is often validated by substitutability tests. If a base-class test fails when using a subclass, then the subclass is violating the contract.

This is a major reason why robust unit tests around the base abstraction are valuable.

---

### Q10: How would you explain LSP to a beginner?

A: “If a child class cannot be used anywhere the parent class is used without surprising the caller, then the child class should not inherit from it.”

That is the simplest practical definition.

---

## Summary

The Liskov Substitution Principle says that subclasses should not violate the expectations set by the parent class.

A good inheritance relationship means:

- the child is truly a valid subtype
- the parent contract remains intact
- polymorphism remains safe
- calling code does not need special cases

When the inheritance tree is built on invalid assumptions, code may compile but still fail in production.

That is why LSP is such an important design principle in clean, reliable object-oriented systems.

---

## Quick revision notes

- LSP = Replaceability without breaking behavior
- Parent contract must remain valid for the child
- Subclass should not change meaning or assumptions of inherited behavior
- If the child needs special handling, maybe inheritance is the wrong modeling choice
- LSP is essential for safe polymorphism and long-term maintainability

---

## Final interview takeaway

When asked in interviews, a strong answer is:

> LSP is about ensuring that subclasses can stand in for their base classes without causing the system to fail. If a subclass changes the semantics of inherited methods or introduces impossible states, it violates LSP.

This is a key idea for writing clean, extensible, and robust object-oriented code.
