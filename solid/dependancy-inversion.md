# Dependency Inversion Principle (DIP)

The **Dependency Inversion Principle** is the `D` in **SOLID**. It helps us design systems in which high-level business rules are not tightly coupled to low-level implementation details such as databases, web frameworks, file systems, or third-party services.

> **High-level modules should not depend on low-level modules. Both should depend on abstractions.**
>
> **Abstractions should not depend on details. Details should depend on abstractions.**

DIP is primarily about the direction of dependencies in the design. It is not the same thing as dependency injection, although dependency injection is a common technique used to implement DIP.

---

## 1. What problem does DIP solve?

Consider an order service that directly creates and uses a concrete MySQL repository:

```java
class MySqlOrderRepository {
    void save(Order order) {
        // SQL and connection details
    }
}

class OrderService {
    private final MySqlOrderRepository repository = new MySqlOrderRepository();

    void place(Order order) {
        // business rules
        repository.save(order);
    }
}
```

`OrderService` is a high-level module: it contains the application business rule. `MySqlOrderRepository` is a low-level module: it contains infrastructure details.

This design creates several problems:

- The business logic is coupled to MySQL.
- Replacing MySQL with PostgreSQL, an API, or an in-memory store requires changing `OrderService`.
- Unit tests need a real database or complicated workarounds.
- The service knows both *what* must happen (save an order) and *how* it happens (using MySQL).
- Infrastructure changes can cause unrelated business code to change.

The dependency arrow points from the policy to the detail:

```text
OrderService  --->  MySqlOrderRepository
business rule       implementation detail
```

That is the dependency direction DIP asks us to improve.

---

## 2. Applying the two rules of DIP

First, define an abstraction around the capability the business rule needs:

```java
interface OrderRepository {
    void save(Order order);
}
```

The high-level service depends on that abstraction:

```java
class OrderService {
    private final OrderRepository repository;

    OrderService(OrderRepository repository) {
        this.repository = repository;
    }

    void place(Order order) {
        // business rules
        repository.save(order);
    }
}
```

Finally, make the infrastructure implementation depend on the abstraction:

```java
class MySqlOrderRepository implements OrderRepository {
    @Override
    public void save(Order order) {
        // SQL and connection details
    }
}
```

The dependency direction is now:

```text
OrderService  --->  OrderRepository  <---  MySqlOrderRepository
business rule       abstraction           implementation detail
```

The business policy owns the interface because it defines the operation it needs. The database adapter conforms to that contract. This is the important inversion: the low-level detail no longer dictates the interface used by the high-level policy.

---

## 3. DIP versus Dependency Injection

These terms are related but different:

| Concept | Meaning |
| --- | --- |
| **Dependency Inversion Principle** | A design principle that says policies and details should depend on abstractions. |
| **Dependency Injection** | A mechanism for supplying an object with its dependencies from outside. |
| **Inversion of Control** | The broader idea that object creation or control flow is delegated to another component or framework. |

Constructor injection implements the principle in the example above:

```java
OrderRepository repository = new MySqlOrderRepository();
OrderService service = new OrderService(repository);
```

The composition root—the application startup or configuration layer—chooses the concrete implementation. The business service does not construct it.

Other injection styles are:

- **Constructor injection:** dependencies are required and visible; usually the safest default.
- **Setter injection:** useful for optional or replaceable dependencies, but the object may be temporarily invalid.
- **Method injection:** a dependency is supplied only for a particular operation.
- **Service locator:** dependencies are looked up globally. This hides requirements and generally makes testing harder, even though it can still provide inversion of control.

---

## 4. The abstraction should belong to the consumer

A common mistake is to create a large interface that mirrors every method of a low-level library:

```java
interface MySqlClient {
    void connect();
    void beginTransaction();
    void executeSql(String sql);
    void createIndex(String name);
    // many database-specific operations
}
```

This exposes infrastructure concepts to the business layer. Instead, the consumer should define a small, intention-revealing interface:

```java
interface OrderRepository {
    Optional<Order> findById(OrderId id);
    void save(Order order);
}
```

This follows the **Interface Segregation Principle** as well: clients depend only on operations they use. It also makes the abstraction stable when the database vendor or persistence strategy changes.

A useful test is to ask: *Can I describe this interface using business language without mentioning the framework or vendor?* If not, the abstraction may be at the wrong boundary.

---

## 5. DIP at architectural boundaries

DIP is particularly valuable at boundaries where change is expected:

- domain logic and persistence
- application code and external HTTP APIs
- business rules and message brokers
- core code and file systems
- UI code and use cases
- payment logic and payment providers
- time, randomness, and environment variables

For example, instead of making business code call Stripe directly:

```java
class CheckoutService {
    private final StripeClient stripe = new StripeClient();

    Receipt checkout(Cart cart) {
        return stripe.charge(cart.totalInCents());
    }
}
```

Define the business capability first:

```java
interface PaymentGateway {
    Receipt charge(Money amount);
}

class CheckoutService {
    private final PaymentGateway payments;

    CheckoutService(PaymentGateway payments) {
        this.payments = payments;
    }

    Receipt checkout(Cart cart) {
        return payments.charge(cart.total());
    }
}

class StripePaymentGateway implements PaymentGateway {
    @Override
    public Receipt charge(Money amount) {
        // Translate the business request into Stripe's API.
    }
}
```

The adapter translates between the domain model and the external model. This keeps vendor-specific errors, request formats, retries, and SDK types at the edge of the application.

---

## 6. Testing benefits

DIP makes tests fast and focused because the high-level module can receive a test double:

```java
class InMemoryOrderRepository implements OrderRepository {
    private final List<Order> orders = new ArrayList<>();

    @Override
    public void save(Order order) {
        orders.add(order);
    }
}

@Test
void placesAnOrder() {
    OrderRepository repository = new InMemoryOrderRepository();
    OrderService service = new OrderService(repository);

    service.place(new Order(/* ... */));

    // Assert the business outcome without starting MySQL.
}
```

The test verifies the policy, not database connectivity. Integration tests can separately verify `MySqlOrderRepository` against a real database. This separation avoids both slow unit tests and false confidence from mocking every detail.

A useful guideline is to mock or fake a boundary that your application owns, rather than mocking every class. The abstraction should represent a meaningful application capability.

---

## 7. DIP is not an excuse to add interfaces everywhere

An interface is useful when it protects a meaningful boundary or when multiple implementations are likely. Introducing an interface for every class can add indirection without reducing coupling.

Avoid abstraction when:

- the class is a simple value object or pure algorithm;
- there is no meaningful boundary or variation point;
- the interface merely repeats one implementation's methods;
- the abstraction is created only to satisfy a mocking tool.

Start with a concrete implementation when the design is simple, and introduce an abstraction when a real boundary appears. The goal is to isolate volatile details, not to maximize the number of interfaces.

---

## 8. Common misconceptions and pitfalls

### DIP does not mean high-level code may never know concrete types

Concrete types belong in the composition root or adapter layer. The problem is allowing business policies to construct and control infrastructure details.

### An interface alone does not create inversion

This is still tightly coupled if the service creates the implementation:

```java
class OrderService {
    private final OrderRepository repository = new MySqlOrderRepository();
}
```

The service now depends on the abstraction at compile time but still controls the concrete detail at runtime. Inject the dependency instead.

### Avoid leaking low-level types through the abstraction

An interface such as `PaymentGateway` should not return `StripeCharge` or accept `SqlConnection`. Use domain-level types and translate at the edge.

### Do not put business rules in adapters

Adapters should translate, persist, publish, or call external systems. Decisions that define the application's business policy should remain in high-level modules.

### Be careful with abstractions owned by frameworks

Framework interfaces can be useful at the boundary, but core business rules should not need to understand framework lifecycle or annotations unless that coupling is intentional.

---

## 9. A practical workflow for applying DIP

1. **Identify the policy.** What business decision or use case must remain stable?
2. **Identify the detail.** Which database, SDK, framework, transport, clock, or file system is likely to change?
3. **Define the required capability.** Write a small interface in the vocabulary of the policy.
4. **Make the policy depend on the interface.** Do not instantiate the detail inside the policy.
5. **Implement the interface at the boundary.** Translate between domain objects and external representations there.
6. **Inject the implementation at the composition root.** Keep object wiring out of business code.
7. **Test the policy independently.** Use a fake or stub for the boundary and integration tests for the adapter.
8. **Review the boundary.** Remove methods and types that expose implementation details.

---

## 10. Summary

Dependency Inversion Principle is about making **business policies stable and infrastructure replaceable**:

- High-level modules depend on abstractions that express their needs.
- Low-level modules implement those abstractions.
- Abstractions are shaped by the consumer, not by the database or vendor SDK.
- Dependency injection supplies implementations from outside the policy.
- Adapters isolate translation and infrastructure concerns.
- Interfaces should be introduced at meaningful boundaries, not mechanically everywhere.

When applying DIP, ask:

> If I replace the database, external provider, framework, or transport, do my business rules need to change?

If the answer is no—or only the composition root and adapter change—the dependency direction is likely healthy.
