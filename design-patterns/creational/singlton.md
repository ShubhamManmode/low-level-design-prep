Singleton Design Pattern
The Singleton pattern is a creational design pattern that guarantees:

A class has only one object instance.
That instance is accessible through a global access point.
The object is created either eagerly or lazily, depending on the implementation.
Typical use cases include:

Configuration managers
Logging services
Cache managers
Database connection pools
Thread pools
Application-wide registries
Feature flag managers
A Singleton is usually unique per JVM and class loader. It does not automatically mean one instance across multiple servers or application processes.

Basic Structure
Text
Client
  |
  | calls getInstance()
  v
Singleton Class
  |
  | Is instance already created?
  |
  +---- Yes ----> Return existing instance
  |
  +---- No -----> Create instance
                    |
                    v
              Store instance
                    |
                    v
              Return instance
Singleton Flow Diagram
Mermaid
flowchart TD
    A[Client calls getInstance] --> B{Does instance exist?}
    B -- Yes --> C[Return existing instance]
    B -- No --> D[Create Singleton object]
    D --> E[Store object in static field]
    E --> C
The constructor must be private so that other classes cannot directly create objects using new.

Basic Singleton Implementation
Singleton.java
public class Singleton {

    private static Singleton instance;

    private Singleton() {
        // Prevent direct object creation
Usage:

Client.java
public class Client {

    public static void main(String[] args) {
        Singleton first = Singleton.getInstance();
        Singleton second = Singleton.getInstance();

The expression:

Java
first == second
compares references. It prints true because both variables point to the same object.

Problem with This Implementation
This implementation is not thread-safe.

Suppose two threads execute getInstance() at the same time:

Text
Thread 1                         Thread 2
   |                                |
   | instance == null               |
   |                                | instance == null
   |                                |
   | new Singleton()                |
   |                                | new Singleton()
   |                                |
   | stores object A                |
   |                                | stores object B
Two objects may be created, violating the Singleton contract.

Thread-Safe Singleton Implementations
1. Eager Initialization
The object is created when the class is loaded.

EagerSingleton.java
public class EagerSingleton {

    private static final EagerSingleton INSTANCE =
            new EagerSingleton();

    private EagerSingleton() {
Advantages
Simple
Thread-safe by default
No synchronization overhead during access
Safe publication by Java class initialization
Disadvantages
Object is created even if it is never used
Not ideal if construction is expensive
Cannot easily handle exceptions during initialization
This approach is suitable when the Singleton object is lightweight and always required.

2. Synchronized Accessor
The complete method is synchronized.

SynchronizedSingleton.java
public class SynchronizedSingleton {

    private static SynchronizedSingleton instance;

    private SynchronizedSingleton() {
    }
Why Is It Thread-Safe?
Only one thread can execute the synchronized method at a time.

Text
Thread 1 enters getInstance()
Thread 2 waits

Thread 1 creates the object
Thread 1 returns the object
Thread 1 exits the method

Thread 2 enters getInstance()
Thread 2 sees the existing object
Thread 2 returns the same object
Disadvantage
Synchronization happens on every call, even after the object has already been created. This may introduce unnecessary overhead in heavily used code.

3. Double-Checked Locking
Double-checked locking avoids synchronization after the Singleton has already been created.

DoubleCheckedSingleton.java
public class DoubleCheckedSingleton {

    private static volatile DoubleCheckedSingleton instance;

    private DoubleCheckedSingleton() {
    }
Flow Diagram
Mermaid
flowchart TD
    A[Thread calls getInstance] --> B{instance == null?}
    B -- No --> C[Return existing instance]
    B -- Yes --> D[Acquire class lock]
    D --> E{instance == null again?}
    E -- No --> F[Release lock and return instance]
    E -- Yes --> G[Create instance]
    G --> H[Assign instance]
    H --> I[Release lock]
    I --> C
Why Are There Two Null Checks?
First check
Java
if (instance == null)
Avoids locking when the object already exists.

Second check
Java
if (instance == null)
Two threads may both pass the first check before either thread acquires the lock. The second check ensures that the object was not created by another thread while the current thread was waiting for the lock.

Why Is volatile Required?
The following statement:

Java
instance = new DoubleCheckedSingleton();
may internally involve:

Text
1. Allocate memory
2. Initialize the object
3. Assign the reference
Without volatile, the JVM or CPU may reorder these operations:

Text
1. Allocate memory
2. Assign reference
3. Initialize object
Another thread may observe a non-null reference to a partially initialized object.

volatile guarantees:

Visibility between threads
Proper ordering of writes
No thread sees a partially constructed object through this reference
Without volatile, double-checked locking is unsafe in Java.

4. Initialization-on-Demand Holder Idiom
This is one of the best lazy Singleton implementations in Java.

HolderSingleton.java
public class HolderSingleton {

    private HolderSingleton() {
    }

    private static class Holder {
How It Works
HolderSingleton is loaded first.
The nested Holder class is not loaded immediately.
The Holder class is loaded only when getInstance() is called.
Java class initialization is thread-safe.
The instance is created exactly once.
Text
Application starts
      |
      v
HolderSingleton class loaded
      |
      v
Holder class not loaded yet
      |
      v
getInstance() called
      |
      v
Holder class loaded
      |
      v
INSTANCE created once
      |
      v
INSTANCE returned
Advantages
Lazy initialization
Thread-safe
No explicit synchronization
No volatile required
Fast access after initialization
Clean and readable
For many Java applications, this is a very good Singleton implementation.

5. Enum Singleton
The enum approach is often considered the safest Java Singleton implementation.

EnumSingleton.java
public enum EnumSingleton {

    INSTANCE;

    public void performOperation() {
        System.out.println("Operation performed");
Usage:

EnumClient.java
public class EnumClient {

    public static void main(String[] args) {
        EnumSingleton first = EnumSingleton.INSTANCE;
        EnumSingleton second = EnumSingleton.INSTANCE;

Why Is Enum Singleton Safe?
Java provides special guarantees for enum instances:

The instance is created only once
Enum construction is thread-safe
Serialization is handled correctly
Reflection cannot normally create another enum instance
The JVM controls enum instance creation
Disadvantages
Cannot extend another class because all enums implicitly extend java.lang.Enum
May be less flexible if the class requires inheritance
Some developers prefer a normal class for clarity
Comparison of Implementations
Implementation	Lazy	Thread-safe	Performance	Main Concern
Simple lazy	Yes	No	Fast	Race condition
Eager initialization	No	Yes	Fast	Object always created
Synchronized method	Yes	Yes	Moderate	Locks every call
Double-checked locking	Yes	Yes	Fast	Requires volatile
Holder idiom	Yes	Yes	Fast	Java-specific
Enum	Effectively yes	Yes	Fast	Cannot extend a class
Recommended choices:

Text
Simple object and always needed:
    Eager Singleton

Lazy initialization:
    Holder idiom

Maximum protection against serialization and reflection:
    Enum Singleton
Singleton and Thread Safety
A thread-safe Singleton must guarantee two things:

1. Only one object is created
Multiple threads must not enter the creation logic simultaneously.

2. The fully initialized object is visible
A thread must never receive a partially constructed object.

This is why the following implementation is unsafe:

UnsafeSingleton.java
public class UnsafeSingleton {

    private static UnsafeSingleton instance;

    private UnsafeSingleton() {
    }
And this implementation is safe:

SafeSingleton.java
public class SafeSingleton {

    private static volatile SafeSingleton instance;

    private SafeSingleton() {
    }
Singleton and Serialization
A normal Singleton can be broken through serialization.

Java
ObjectOutputStream output =
        new ObjectOutputStream(new FileOutputStream("singleton.ser"));

output.writeObject(singleton);
When the object is deserialized, Java may create a new object instead of returning the existing Singleton instance.

To prevent this, define readResolve():

SerializableSingleton.java
import java.io.Serializable;

public class SerializableSingleton implements Serializable {

    private static final long serialVersionUID = 1L;

readResolve() tells Java to return the existing instance after deserialization.

The enum implementation handles serialization automatically.

Singleton and Reflection
Reflection can access a private constructor:

ReflectionExample.java
import java.lang.reflect.Constructor;

public class ReflectionExample {

    public static void main(String[] args) throws Exception {
        Constructor<HolderSingleton> constructor =
This can create another instance and break a normal Singleton.

A defensive constructor can detect repeated construction:

DefensiveSingleton.java
public class DefensiveSingleton {

    private static boolean instanceCreated = false;

    private DefensiveSingleton() {
        if (instanceCreated) {
However, reflection-based attacks can be complex, and the enum implementation is generally more resistant.

Singleton and Cloning
If a Singleton implements Cloneable, cloning may create another object.

CloneSafeSingleton.java
public class CloneSafeSingleton implements Cloneable {

    private static final CloneSafeSingleton INSTANCE =
            new CloneSafeSingleton();

    private CloneSafeSingleton() {
Another option is to return the existing instance from clone(), but preventing cloning is usually clearer.

Important Limitation: Singleton Is Not Globally Unique
A Singleton usually guarantees one instance per:

JVM
Class loader
Application context
It does not guarantee one instance across:

Multiple application servers
Multiple Docker containers
Multiple JVM processes
Multiple machines
Distributed systems
For example:

Text
Server A -> Singleton instance A
Server B -> Singleton instance B
Server C -> Singleton instance C
If only one instance is required across multiple machines, use a distributed coordination mechanism such as:

Database locking
Redis-based locks
ZooKeeper
Consul
etcd
Advantages
Controlled object creation
One shared object
Lazy initialization is possible
Convenient global access
Can reduce unnecessary object creation
Disadvantages
Introduces global state
Makes unit testing more difficult
Can hide dependencies
May violate the Single Responsibility Principle
Can become a large “god object”
Creates tight coupling
Often makes parallel testing harder
Does not provide distributed uniqueness
Instead of directly accessing a Singleton everywhere:

Java
Logger.getInstance().log("message");
Prefer dependency injection where practical:

Service.java
public class Service {

    private final Logger logger;

    public Service(Logger logger) {
        this.logger = logger;
Dependency injection makes the class easier to test and maintain.

Interview Follow-Up Questions
1. Why is the Singleton constructor private?
To prevent clients from creating objects directly with:

Java
new Singleton();
The class controls its own object creation.

2. Why is the Singleton instance static?
The getInstance() method is usually static, so the instance must be associated with the class rather than with an object.

A static field also provides one shared storage location.

3. Why do we use volatile in double-checked locking?
volatile prevents instruction reordering and guarantees visibility of the fully initialized object across threads.

4. Why are there two instance == null checks?
The first check avoids synchronization after initialization. The second check prevents multiple objects from being created by threads that were waiting for the lock.

5. Which Singleton implementation do you prefer?
A strong answer would be:

For lazy initialization in Java, I prefer the initialization-on-demand holder idiom because it is lazy, thread-safe through class initialization, fast, and does not require explicit synchronization. If serialization and reflection resistance are especially important, I prefer an enum Singleton.

6. Can Singleton be broken?
Yes, through:

Reflection
Serialization
Cloning
Multiple class loaders
Incorrectly implemented inheritance
Multiple JVM processes
Enum Singleton protects against many of these issues.

7. Is Singleton thread-safe by default?
No.

Thread safety depends on the implementation. A simple lazy implementation is not thread-safe, while eager initialization, the holder idiom, synchronized access, and enum Singleton are thread-safe.

8. Is Singleton the same as static class?
No.

A Singleton:

Is an object
Can implement interfaces
Can be passed as a dependency
Can have instance methods
Can support polymorphism
A static class generally cannot be instantiated or used through an interface in the same way.

9. Can we inherit from a Singleton?
Technically, inheritance is possible if the class is not final, but it can complicate the guarantee that only one object exists.

A Singleton constructor is private, so subclassing is normally restricted. Usually, prefer composition over inheritance for Singleton classes.

10. Why can Singleton make testing difficult?
Because all tests share the same object and state:

Text
Test 1 modifies Singleton state
        |
        v
Test 2 receives modified state
This can cause:

Test order dependency
State leakage
Difficult mocking
Parallel test failures
Dependency injection is usually a better design for services that need to be mocked.

11. How would you test a Singleton?
SingletonTest.java
import static org.junit.jupiter.api.Assertions.assertSame;
import org.junit.jupiter.api.Test;

public class SingletonTest {

    @Test
For thread safety, create many threads and verify that every thread receives the same reference.

12. When should Singleton not be used?
Avoid Singleton when:

The object is stateless and static utility methods are sufficient
Multiple instances may be required later
The object needs frequent mocking
The object contains mutable global state
The application is distributed and uniqueness is required across machines
Dependency injection can solve the problem more cleanly
Recommended Production Implementation
For a normal Java application, the holder idiom is concise and effective:

ProductionSingleton.java
public final class ProductionSingleton {

    private ProductionSingleton() {
    }

    private static class InstanceHolder {
For the strongest built-in Java guarantees, use an enum:

ProductionEnumSingleton.java
public enum ProductionEnumSingleton {

    INSTANCE;

    public void execute() {
        // Business operation
The key point is that Singleton is not merely about using a static variable. A correct implementation must consider concurrent creation, safe publication, serialization, reflection, cloning, testing, and application boundaries.

singlton.md
