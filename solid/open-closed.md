# Open-Closed Principle (OCP)

## Definition

The **Open-Closed Principle** states that software entities (classes, modules, functions) should be:
- **Open for extension**: You should be able to add new functionality
- **Closed for modification**: Existing code should not need to be modified when adding new features

## Core Concept

OCP encourages designing systems that can evolve and grow without changing the existing codebase. This reduces the risk of breaking existing functionality and makes the system more maintainable.

## Key Benefits

1. **Reduced Risk**: Changes don't affect existing code
2. **Better Maintainability**: Less code modification needed
3. **Improved Reusability**: Classes are designed to be extended
4. **Flexible Architecture**: Easy to add new features
5. **Testability**: Less need to re-test existing code

## Violation Example

```java
// ❌ BAD: Violates OCP - Need to modify PaymentProcessor for each new payment type
class PaymentProcessor {
    public void processPayment(String paymentType, double amount) {
        if (paymentType.equals("creditCard")) {
            processCreditCard(amount);
        } else if (paymentType.equals("paypal")) {
            processPayPal(amount);
        } else if (paymentType.equals("bitcoin")) {
            processBitcoin(amount);
        }
        // Adding new payment type requires modifying this class
    }
    
    private void processCreditCard(double amount) {
        System.out.println("Processing credit card: $" + amount);
    }
    
    private void processPayPal(double amount) {
        System.out.println("Processing PayPal: $" + amount);
    }
    
    private void processBitcoin(double amount) {
        System.out.println("Processing Bitcoin: $" + amount);
    }
}

// ✅ GOOD: Follows OCP - Open for extension, closed for modification

// Abstract base for payment strategies
abstract class PaymentMethod {
    abstract void process(double amount);
}

// Concrete implementations
class CreditCardPayment extends PaymentMethod {
    @Override
    public void process(double amount) {
        System.out.println("Processing credit card: $" + amount);
    }
}

class PayPalPayment extends PaymentMethod {
    @Override
    public void process(double amount) {
        System.out.println("Processing PayPal: $" + amount);
    }
}

class BitcoinPayment extends PaymentMethod {
    @Override
    public void process(double amount) {
        System.out.println("Processing Bitcoin: $" + amount);
    }
}

// Processor class - No need to modify for new payment types
class PaymentProcessor {
    private PaymentMethod paymentMethod;
    
    public PaymentProcessor(PaymentMethod paymentMethod) {
        this.paymentMethod = paymentMethod;
    }
    
    public void processPayment(double amount) {
        paymentMethod.process(amount);
    }
}

// Easy to add new payment type without modifying existing code
class ApplePayPayment extends PaymentMethod {
    @Override
    public void process(double amount) {
        System.out.println("Processing Apple Pay: $" + amount);
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        PaymentProcessor processor1 = new PaymentProcessor(new CreditCardPayment());
        processor1.processPayment(100);
        
        PaymentProcessor processor2 = new PaymentProcessor(new PayPalPayment());
        processor2.processPayment(50);
        
        // New payment type added without changing PaymentProcessor
        PaymentProcessor processor3 = new PaymentProcessor(new ApplePayPayment());
        processor3.processPayment(75);
    }
}


Advantages:

PaymentProcessor is closed for modification
New payment methods can be added without changing existing code
Each payment type is independent
Easy to test each payment method separately


Techniques to Achieve OCP
1. Abstraction (Interfaces/Abstract Classes)
Define contracts that implementations extend
Allows new implementations without modifying existing code
2. Polymorphism
Use method overriding to provide different behaviors
Runtime dispatch handles new types automatically
3. Dependency Injection
Inject dependencies rather than hardcoding them
Allows swapping implementations easily
4. Strategy Pattern
Encapsulate algorithms in separate classes
Select strategy at runtime
5. Template Method Pattern
Define skeleton in base class
Subclasses fill in specific steps
6. Decorator Pattern
Add responsibilities to objects dynamically
Without modifying original class
OCP vs Other SOLID Principles
Principle	Relationship
SRP	Complements OCP; focused classes are easier to extend
LSP	Ensures extensions maintain contracts correctly
ISP	Helps OCP by providing focused interfaces
DIP	Supports OCP through abstraction and loose coupling
Common Pitfalls
❌ Over-Engineering: Don't make everything extensible; predict likely extensions

❌ Ignoring SRP: Extensions should have single responsibility

❌ Hardcoding Dependencies: Use injection or configuration

❌ Poor Abstraction: Abstract based on actual use cases, not theory

Balancing Act
OCP requires balance:

Code
┌─────────────────────────────────────┐
│      Open-Closed Principle          │
├─────────────────────────────────────┤
│ Too Rigid          │    Too Flexible │
│ (Over-closed)      │  (Over-opened)  │
│                    │                 │
│ • Hard to change   │ • Over-complex  │
│ • Inflexible       │ • Hard to track │
│ • Tightly coupled  │ • Performance   │
│                    │    impact       │
│ ◄────── Sweet Spot ──────►           │
└─────────────────────────────────────┘
Practical Checklist
 Can I add new functionality without modifying existing classes?
 Are extensions achieved through abstraction/interfaces?
 Is there minimal impact on existing tests?
 Is the design flexible but not over-engineered?
 Are dependencies injected rather than hardcoded?
 Does each class have a single reason to change?
