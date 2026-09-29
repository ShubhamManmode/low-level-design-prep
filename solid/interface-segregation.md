# Interface Segregation Principle (ISP)

## Definition

The Interface Segregation Principle states:

> "Clients should not be forced to depend on interfaces they do not use."

In simple words, a class should not implement methods it never needs. If one large interface contains many responsibilities, then every implementing class must implement all methods—even the ones unrelated to its purpose. This creates tightly coupled, fragile, and hard-to-maintain code.

A good design splits large interfaces into smaller, specific ones so each class implements only the methods relevant to it.

---

## Why this principle exists

Large interfaces often start as a shortcut. A developer creates one general-purpose interface like:

```java
public interface Machine {
    void print();
    void scan();
    void fax();
    void copy();
}
```

This seems convenient at first, because every machine can implement the same interface. But in reality, not every machine does all of these actions.

For example:

```java
public class SimplePrinter implements Machine {
    @Override
    public void print() {
        System.out.println("Printing document");
    }

    @Override
    public void scan() {
        throw new UnsupportedOperationException("Scanner not available");
    }

    @Override
    public void fax() {
        throw new UnsupportedOperationException("Fax not available");
    }

    @Override
    public void copy() {
        throw new UnsupportedOperationException("Copy not available");
    }
}
```

This is a direct violation of ISP. The class is forced to implement methods it cannot support.

---

## The problem with fat interfaces

A fat interface is an interface that tries to do too many jobs at once. When many classes implement it, they end up with:

- Empty or dummy implementations
- UnsupportedOperationException
- Unused methods
- Unclear API contracts
- Harder maintenance
- Increased coupling

This is especially common in systems where a single interface was created for convenience but later grew as new features were added.

---

## ISP in terms of clients

The principle is not about interfaces being small by default. It is about clients only depending on the methods they actually need.

A client is any class or module using an interface. If a client is forced to depend on methods unrelated to its purpose, the interface is too broad.

That means the design should look like this:

```java
public interface Printer {
    void print();
}

public interface Scanner {
    void scan();
}

public interface FaxMachine {
    void fax();
}

public interface Copier {
    void copy();
}
```

Now each class implements only the interfaces relevant to it:

```java
public class SimplePrinter implements Printer {
    @Override
    public void print() {
        System.out.println("Printing document");
    }
}

public class MultiFunctionPrinter implements Printer, Scanner, FaxMachine, Copier {
    @Override
    public void print() {
        System.out.println("Printing");
    }

    @Override
    public void scan() {
        System.out.println("Scanning");
    }

    @Override
    public void fax() {
        System.out.println("Faxing");
    }

    @Override
    public void copy() {
        System.out.println("Copying");
    }
}
```

This is more accurate and cleaner. Each class only knows about the capabilities it truly supports.

---

## Real-world analogy

Imagine a restaurant ordering system:

```java
public interface Employee {
    void takeOrder();
    void cookFood();
    void serveFood();
    void cleanTables();
    void managePayroll();
}
```

Now every employee has to implement all methods. A waiter would need to know payroll management, which makes no sense. A cook would need to serve tables. This is exactly what ISP prevents.

Better design:

```java
public interface Waiter {
    void takeOrder();
    void serveFood();
}

public interface Cook {
    void cookFood();
}

public interface Cleaner {
    void cleanTables();
}

public interface Manager {
    void managePayroll();
}
```

Each role depends only on the responsibilities it needs.

---

## Benefits of following ISP

### 1. Cleaner code
Each class has a focused responsibility and a small contract.

### 2. Less unnecessary coupling
A class is not forced to know about unrelated behavior.

### 3. Easier testing
You can test a printer without needing scan or fax logic.

### 4. Better maintainability
Changing one interface does not break unrelated implementations.

### 5. More flexibility
New implementations can pick only the capabilities they support.

### 6. Fewer runtime surprises
You avoid throwing unsupported exceptions just to satisfy a big contract.

---

## Common violation patterns

Some common examples of ISP violations are:

- One interface named `Worker` containing `work()`, `eat()`, `sleep()`, `drive()`, `analyze()`
- One `Repository` interface with create, update, delete, export, import, sync, backup
- One `PaymentService` interface with `pay()`, `refund()`, `chargeBack()`, `scheduleRecurringPayment()`, `generateInvoice()`
- `Animal` interface with `fly()`, `swim()`, `run()`, `bark()`, `meow()`

These interfaces are often too broad because they try to describe several unrelated behaviors under one umbrella.

---

## ISP and Dependency Inversion Principle (DIP)

ISP is closely related to DIP. Both encourage designing interfaces around client needs instead of forcing all implementations to obey a giant contract.

If a class depends on a broad interface, it becomes fragile. When the interface changes, all implementers must adapt. By splitting interfaces, you reduce the blast radius of change.

---

## Example: bad design vs good design

### Bad design

```java
public interface UserOperations {
    void createUser();
    void deleteUser();
    void resetPassword();
    void sendEmail();
    void generateReport();
    void exportData();
}
```

```java
public class AdminService implements UserOperations {
    public void createUser() {}
    public void deleteUser() {}
    public void resetPassword() {}
    public void sendEmail() {}
    public void generateReport() {}
    public void exportData() {}
}
```

```java
public class SimpleUserService implements UserOperations {
    public void createUser() {}
    public void deleteUser() {}
    public void resetPassword() {}
    public void sendEmail() {
        throw new UnsupportedOperationException("Email not supported");
    }
    public void generateReport() {
        throw new UnsupportedOperationException("Reports not supported");
    }
    public void exportData() {
        throw new UnsupportedOperationException("Export not supported");
    }
}
```

### Good design

```java
public interface UserManager {
    void createUser();
    void deleteUser();
    void resetPassword();
}

public interface EmailSender {
    void sendEmail();
}

public interface ReportGenerator {
    void generateReport();
}

public interface DataExporter {
    void exportData();
}
```

Now each service implements only what it truly needs.

---

## When to apply ISP

Use the Interface Segregation Principle when:

- a class implements an interface but does not use most methods
- interfaces are growing with unrelated methods
- multiple implementations can only support subsets of the same contract
- a change in one interface requires unrelated classes to update
- you see `UnsupportedOperationException` or empty method stubs

---

## Important note: don't over-separate everything

ISP is useful, but it should not lead to interface explosion. Splitting an interface into dozens of tiny interfaces may also reduce readability.

The goal is balance:

- keep interfaces cohesive
- separate responsibilities that are genuinely different
- avoid forcing unrelated clients to depend on each other

A good rule: if two methods are unrelated for most clients, they probably belong in different interfaces.

---

## Summary

The Interface Segregation Principle says:

- do not force large, unrelated responsibilities into one interface
- split interfaces based on client needs
- let classes implement only the methods they actually use

This leads to simpler code, fewer hacks, better maintainability, and cleaner object design.

In short, ISP is about respecting boundaries. Small, focused interfaces reduce coupling and make software easier to evolve.

---

## Key takeaway

"A client should not be forced to depend on methods it does not use."

That is the heart of the Interface Segregation Principle.

If you apply this principle well, your code becomes more modular, more flexible, and much easier to extend over time.
