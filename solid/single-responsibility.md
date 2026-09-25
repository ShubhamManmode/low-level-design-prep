# Single Responsibility Principle (SRP)

> A class should have only one reason to change.

This is the first of the five SOLID principles. It tells us that a class should focus on a single concern or responsibility instead of doing many unrelated jobs. If one requirement changes, the class should change for one reason only.

---

## Why this principle matters

In real applications, classes often start small and become crowded with responsibilities:

- validation logic
- business rules
- database access
- logging
- notifications
- formatting and rendering
- user input handling

Once that happens, the class becomes harder to:

- understand
- test
- reuse
- extend
- maintain

A class with many responsibilities becomes fragile. A small change in one area may accidentally break another part of the same class.

---

## Intuition behind SRP

The word responsibility is not just “a task.” It is a reason to change.

If a class has multiple reasons to change, then it violates SRP.

For example:

- `User` class may need to change because:
  - user profile fields changed
  - validation rules changed
  - persistence logic changed
  - email notifications changed

That is multiple reasons to change, so the class is doing too much.

---

## Simple definition

SRP says:

A module, class, or function should be responsible for one thing, and that thing should be the only reason it needs to change.

This does not mean a class should have only one method or only one field. It means the class should represent one cohesive concept and avoid mixing unrelated behaviours.

---

## Example of SRP violation

```java
class Invoice {
    private String customerName;
    private double amount;

    public Invoice(String customerName, double amount) {
        this.customerName = customerName;
        this.amount = amount;
    }

    public void saveToDatabase() {
        // insert into DB
    }

    public void sendEmail() {
        // send invoice email
    }

    public void printInvoice() {
        // print invoice to console or printer
    }

    public double calculateTotal() {
        return amount + (amount * 0.18);
    }
}
```

This class has several responsibilities:

- business logic (`calculateTotal`)
- persistence (`saveToDatabase`)
- communication (`sendEmail`)
- presentation (`printInvoice`)

Why is this a problem?

- changing database logic may require updating invoice logic
- changing email format may require modifying the invoice object
- printing changes may affect storage or calculation

This is a clear SRP violation.

---

## Better design

Split responsibilities into separate classes.

```java
class Invoice {
    private String customerName;
    private double amount;

    public double calculateTotal() {
        return amount + (amount * 0.18);
    }
}

class InvoiceRepository {
    public void save(Invoice invoice) {
        // save invoice to database
    }
}

class InvoiceEmailService {
    public void sendInvoiceEmail(Invoice invoice) {
        // send email
    }
}

class InvoicePrinter {
    public void print(Invoice invoice) {
        // print invoice
    }
}
```

Now each class has a single responsibility:

- `Invoice`: invoice data and business calculation
- `InvoiceRepository`: persistence
- `InvoiceEmailService`: sending emails
- `InvoicePrinter`: formatting/printing

If the email system changes, only `InvoiceEmailService` changes.

---

## A more realistic example: user management

```java
class UserManager {
    public void registerUser(String email, String password) {
        // validate input
        // create user
        // save in database
        // send welcome email
        // log activity
    }
}
```

This is too many responsibilities in one place.

Refactor as:

```java
class UserValidator {
    public boolean isValidEmail(String email) { return email.contains("@"); }
}

class UserRepository {
    public void save(User user) { /* DB */ }
}

class EmailService {
    public void sendWelcomeEmail(User user) { /* email */ }
}

class AuditLogger {
    public void log(String message) { /* log */ }
}
```

Each class has a clear purpose.

---

## SRP is about cohesion, not just count of methods

A common misconception is: “If a class has only one method, it is SRP compliant.”

That is not necessarily true.

A class can still violate SRP even with a single method if the method mixes unrelated concerns.

Example:

```java
class OrderProcessor {
    public void process(Order order) {
        validate(order);
        saveToDb(order);
        sendNotification(order);
        generateReport(order);
    }
}
```

Even though there is one method, the class is handling validation, persistence, notifications, and reporting.

The principle is about cohesion and reasoning, not method count.

---

## When SRP is violated

SRP is often violated when a class does all of the following:

- accepts data and stores it
- validates it
- updates a database
- sends notifications
- logs analytics
- formats output

This often happens when a developer adds “just one more feature” to an existing class instead of creating a new abstraction.

---

## How to identify SRP violations

Ask these questions:

- What are the different reasons this class would change?
- Does this class represent more than one concept?
- Are there multiple unrelated behaviors grouped together?
- If one requirement changes, does the class need to change for unrelated reasons?

If the answer is yes to multiple categories, it's a candidate for refactoring.

---

## Benefits of following SRP

### 1. Easier maintenance

You isolate changes to one component rather than editing a large class.

### 2. Better readability

Each class has a clear purpose, so code becomes easier to understand.

### 3. Better testability

Smaller, focused classes are easier to test in isolation.

### 4. Lower coupling

Classes depend on smaller, more focused responsibilities rather than huge, interconnected objects.

### 5. Reduced risk

A change in one subsystem is less likely to unintentionally affect unrelated features.

---

## Trade-offs and practical advice

SRP is powerful, but it should be applied with balance.

Over-splitting a system into too many small classes can create:

- unnecessary indirection
- too many objects
- complex orchestration
- poor readability due to class explosion

The goal is not “one class per method.” The goal is to separate meaningful responsibilities without creating a sprawling design.

A good rule:

- Extract responsibilities only when they are truly distinct or likely to change independently.
- Keep related behavior together when it belongs to the same concept.

---

## Example of a good SRP-friendly design

```java
class Order {
    private String id;
    private double total;

    public double getTotal() {
        return total;
    }
}

class OrderService {
    public void createOrder(Order order) {
        // business workflow
    }
}

class PaymentGateway {
    public void charge(Order order) {
        // payment processing
    }
}

class OrderRepository {
    public void save(Order order) {
        // database save
    }
}
```

This structure makes it easy to change payment processing without affecting order persistence or business flow.

---

## SRP in system design

SRP is not just about classes. It also applies to modules, services, and components.

For example:

- `UserService` should not also handle email, billing, analytics, and database access
- `PaymentService` should not also create invoices and send notifications
- `ReportGenerator` should not also manage database queries and file export

This principle scales from a single class to a whole architecture.

---

## Real-world analogy

Think of a company department:

- HR handles hiring and employee records
- Finance handles pay and accounting
- Support handles customer issues

If one department does everything, responsibilities overlap and communication becomes messy. The same is true in software.

A well-designed class should behave like a focused team unit, not a general-purpose “do everything” utility.

---

## Common anti-patterns

### God class

A class that grows into a kitchen sink of unrelated methods.

Example:

```java
class SystemController {
    public void login() {}
    public void saveUser() {}
    public void sendEmail() {}
    public void generateReport() {}
    public void parseCsv() {}
    public void exportJson() {}
}
```

This is a classic SRP violation.

---

## Quick checklist

Use this checklist before accepting a class design:

- Does the class have one clear purpose?
- If the requirements change, is there only one reason to modify it?
- Are unrelated responsibilities mixed together?
- Are methods tightly connected to the same concept?
- Could the class be split into two clearer abstractions?

If the answer is mostly “no,” the class probably violates SRP.

---

## Final takeaway

The Single Responsibility Principle is not about making classes artificially tiny. It is about making them conceptually focused.

A class should represent one responsibility, and one reason to change.

When followed well, SRP leads to cleaner code, better architecture, and easier maintenance. It is a foundation for writing robust, scalable software systems.

---

## Summary

- SRP = one reason to change
- focus on a single concern
- separate unrelated logic
- keep cohesive responsibilities together
- avoid “god classes”
- refactor when behavior changes for different reasons

This principle is one of the cornerstones of maintainable software design.

---

## Interview-style answer

> The Single Responsibility Principle says that a class should have only one reason to change. In other words, it should focus on a single responsibility or cohesive behavior. If a class handles business logic, database access, and email sending together, it violates SRP because any change in those areas would require modifying the same class. By splitting responsibilities into focused classes, the design becomes easier to understand, test, extend, and maintain.

---

## Practice question

Imagine a `Student` class with methods like:

- `calculateFee()`
- `saveStudent()`
- `sendNotification()`
- `generateReport()`

Does this class follow SRP? Why or why not?

Answer: It does not follow SRP because it has multiple responsibilities: fee calculation, persistence, notification, and reporting. These are different reasons to change, so the class should be split into smaller focused components.

---

## Key phrase to remember

“A class should do one thing and do it well — but not necessarily only one method.”

That captures the spirit of SRP without turning it into dogma.

---

## The essence of SRP in one sentence

If a class can be changed for two different reasons, it is doing too much.

That is the heart of the Single Responsibility Principle.

---

## Bonus: SRP and dependency injection

When responsibilities are separated, dependency injection becomes much cleaner.

Example:

```java
class OrderController {
    private final OrderService orderService;
    private final EmailService emailService;

    public OrderController(OrderService orderService, EmailService emailService) {
        this.orderService = orderService;
        this.emailService = emailService;
    }
}
```

Now the controller does not know how to save data or send emails directly. It delegates responsibilities, which is exactly what SRP encourages.

---

## Final note

SRP is often the first principle new developers learn because it is easy to grasp and immediately useful. But its real power appears when systems become larger and harder to maintain. A class that follows SRP is easier to reason about, safer to extend, and more stable in the long run.

This is why SRP is considered a foundational design principle in object-oriented programming and software architecture.

---

## References

- Robert C. Martin, “Clean Code”
- Robert C. Martin, “Agile Software Development, Principles, Patterns, and Practices”
- SOLID design principles in object-oriented programming



