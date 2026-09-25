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
