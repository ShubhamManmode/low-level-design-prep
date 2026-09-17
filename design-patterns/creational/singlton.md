# Singleton Design Pattern

The Singleton pattern is a creational design pattern that ensures a class has exactly one instance and provides a global access point to it.

It is commonly used when we need one shared resource such as:

- Configuration manager
- Logging utility
- Cache manager
- Thread pool
- Database connection manager
- Application-wide registry

> Singleton means one instance per JVM/class loader in the normal application context. It does not guarantee a single instance across multiple servers, processes, or distributed systems.

---

## Why Use Singleton?

Singleton is useful when:

- You want to avoid creating multiple unnecessary objects.
- You need a single shared resource across the application.
- You want centralized control over object creation.

Example:

```java
public class AppConfig {
    private static AppConfig instance;

    private AppConfig() {
    }

    public static AppConfig getInstance() {
        if (instance == null) {
            instance = new AppConfig();
        }
        return instance;
    }
}
```

---

## Core Idea

A Singleton class should have:

1. A private constructor
2. A static instance variable
3. A public static method to get the instance

This prevents external code from creating additional objects.

---

## Flow Diagram

```mermaid
flowchart TD
    A[Client calls getInstance()] --> B{Is instance created?}
    B -- Yes --> C[Return existing instance]
    B -- No --> D[Create object]
    D --> E[Store in static variable]
    E --> C
```

---

## Basic Singleton Implementation

```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {
        // private constructor prevents instantiation
    }

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

### Usage

```java
public class Client {
    public static void main(String[] args) {
        Singleton s1 = Singleton.getInstance();
        Singleton s2 = Singleton.getInstance();

        System.out.println(s1 == s2); // true
    }
}
```

This works because both references point to the same object.

---

## Problem in the Basic Version

The above implementation is not thread-safe.

If two threads call `getInstance()` simultaneously, both may see `instance == null` and create two objects.

```text
Thread 1                     Thread 2
  |                            |
  | instance == null           |
  |                            | instance == null
  |                            |
  | new Singleton()            |
  |                            | new Singleton()
  |                            |
  | stores object A            |
  |                            | stores object B
```

This breaks the Singleton rule.

---

## Thread-Safe Singleton Implementations

### 1) Eager Initialization

The instance is created when the class is loaded.

```java
public class EagerSingleton {
    private static final EagerSingleton INSTANCE = new EagerSingleton();

    private EagerSingleton() {
    }

    public static EagerSingleton getInstance() {
        return INSTANCE;
    }
}
```

#### Advantages

- Very simple
- Thread-safe by default
- Fast access

#### Disadvantages

- Object is created even if never used
- Not ideal if creation is expensive

---

### 2) Lazy Initialization with Synchronization

```java
public class SynchronizedSingleton {
    private static SynchronizedSingleton instance;

    private SynchronizedSingleton() {
    }

    public static synchronized SynchronizedSingleton getInstance() {
        if (instance == null) {
            instance = new SynchronizedSingleton();
        }
        return instance;
    }
}
```

#### Why it is thread-safe

Only one thread can execute the synchronized method at a time.

#### Drawback

Every call to `getInstance()` acquires the lock, even after initialization, which reduces performance.

---

### 3) Double-Checked Locking

This avoids the overhead of synchronization after initialization.

```java
public class DoubleCheckedSingleton {
    private static volatile DoubleCheckedSingleton instance;

    private DoubleCheckedSingleton() {
    }

    public static DoubleCheckedSingleton getInstance() {
        if (instance == null) {
            synchronized (DoubleCheckedSingleton.class) {
                if (instance == null) {
                    instance = new DoubleCheckedSingleton();
                }
            }
        }
        return instance;
    }
}
```

#### Why `volatile`?

Without `volatile`, another thread may see a partially initialized object due to instruction reordering.

### Flow Diagram for Double-Checked Locking

```mermaid
flowchart TD
    A[Thread calls getInstance()] --> B{instance == null?}
    B -- No --> C[Return existing instance]
    B -- Yes --> D[Acquire lock]
    D --> E{instance == null again?}
    E -- No --> F[Return instance]
    E -- Yes --> G[Create object]
    G --> H[Assign to instance]
    H --> I[Release lock]
    I --> C
```

#### Why two null checks?

- First check: fast path, avoid locking
- Second check: important inside synchronized block, prevents duplicate creation

---

### 4) Initialization-on-Demand Holder Idiom (Recommended)

This is one of the best lazy initialization patterns in Java.

```java
public class HolderSingleton {
    private HolderSingleton() {
    }

    private static class Holder {
        private static final HolderSingleton INSTANCE = new HolderSingleton();
    }

    public static HolderSingleton getInstance() {
        return Holder.INSTANCE;
    }
}
```

#### Why this is great

- Lazy initialization
- Thread-safe
- No explicit synchronization
- No `volatile` required
- Good performance

This pattern is widely recommended in Java.

---

### 5) Enum Singleton (Best for Java)

```java
public enum EnumSingleton {
    INSTANCE;

    public void doWork() {
        System.out.println("Singleton works");
    }
}
```

Usage:

```java
public class Client {
    public static void main(String[] args) {
        EnumSingleton s1 = EnumSingleton.INSTANCE;
        EnumSingleton s2 = EnumSingleton.INSTANCE;

        System.out.println(s1 == s2); // true
        s1.doWork();
    }
}
```

#### Why enum is strong

- Thread-safe by default
- Only one instance exists
- Serialization-safe
- Reflection-safe in practice

This is often considered the best Singleton implementation in Java.

---

## Singleton vs Static Class

A static class is not the same as a Singleton.

### Singleton

- One object instance exists
- Can implement interfaces
- Can be passed around
- Can be mocked more easily in tests

### Static class

- No instance is created
- Methods are called using the class name
- Harder to extend or inject

---

## Advantages of Singleton

- One shared instance across the application
- Prevents duplicate object creation
- Good for resources like cache and configuration
- Saves memory by avoiding repeated object creation
- Centralized access

---

## Disadvantages of Singleton

- Introduces global state
- Can hide dependencies
- Harder to test in unit tests
- Can lead to tight coupling
- Not ideal for distributed systems
- Can become a “god object” if overused

---

## What Can Break Singleton?

Singleton may be broken if not handled carefully.

### 1) Reflection

Using reflection, private constructors can sometimes be accessed.

### 2) Serialization

A serialized Singleton may create another instance unless `readResolve()` is implemented.

### 3) Cloning

If the class implements `Cloneable`, cloning can create another instance.

### 4) Multiple class loaders

Different class loaders may create different Singleton instances.

### 5) Multiple JVMs / distributed systems

A Singleton is usually one per JVM, not across servers.

---

## Serialization Fix Example

```java
import java.io.Serializable;

public class SerializableSingleton implements Serializable {
    private static final long serialVersionUID = 1L;

    private static final SerializableSingleton INSTANCE = new SerializableSingleton();

    private SerializableSingleton() {
    }

    public static SerializableSingleton getInstance() {
        return INSTANCE;
    }

    protected Object readResolve() {
        return INSTANCE;
    }
}
```

---

## Practical Interview Answer

A strong interview answer is:

> Singleton ensures a class has only one instance and provides a global point of access to it. It is useful for shared resources such as configuration, logging, and caches. To make it thread-safe, use synchronized access, double-checked locking, or the holder idiom. In Java, the enum-based Singleton is often considered the best implementation.

---

## Interview Follow-Up Questions

### 1. Why is the constructor private?

To prevent object creation from outside the class.

### 2. Why is the instance static?

Because it belongs to the class and is shared across all callers.

### 3. Is the simple Singleton thread-safe?

No. The naive lazy implementation is not thread-safe.

### 4. Why use `volatile`?

To avoid visibility issues in double-checked locking.

### 5. Which Singleton approach is best in Java?

Usually:

- Holder idiom for a normal class
- Enum for the strongest built-in guarantee

### 6. When should you avoid Singleton?

When you need multiple instances, want easier testing, or need dependency injection.

---

## Best Recommendation

For production Java code, prefer:

```java
public final class ProductionSingleton {
    private ProductionSingleton() {
    }

    private static class Holder {
        private static final ProductionSingleton INSTANCE = new ProductionSingleton();
    }

    public static ProductionSingleton getInstance() {
        return Holder.INSTANCE;
    }
}
```

or, if you want the strongest built-in guarantee:

```java
public enum ProductionEnumSingleton {
    INSTANCE;
}
```

---

## Summary

The Singleton pattern:

- restricts object creation to one instance
- provides a global access point
- is ideal for shared resources
- must be implemented carefully for thread safety
- should be used thoughtfully because it introduces global state

This is a very common low-level design pattern and is frequently asked in interviews.

---

## Quick Revision

- Singleton = one instance only
- Constructor is private
- Instance is static
- `getInstance()` returns the same object
- Thread safe implementations: synchronized, double-checked locking, holder idiom, enum
- Best Java choice: lazy holder idiom or enum

If you want, I can also provide:

1. a short 2-minute interview version,
2. a real-world Java example,
3. a diagram-only explanation, or
4. a code snippet for multithreading interview questions.
