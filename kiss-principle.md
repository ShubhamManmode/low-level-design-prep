# KISS Principle — Keep It Simple

## 1. What is the KISS Principle?

**KISS** stands for **"Keep It Simple, Stupid"**.

Some variations use:

- Keep It Short and Simple
- Keep It Super Simple

The core idea is:

> **Prefer the simplest solution that correctly solves the problem.**

KISS is a design principle used in:

- Software development
- System design
- Engineering
- Architecture
- UI/UX design
- Process design

The goal is not to make everything extremely short or remove necessary structure. The goal is to **avoid unnecessary complexity**.

---

# 2. Simple Example

Suppose we need to check whether a string is empty.

### Over-engineered solution

```csharp
public bool IsValid(string input)
{
    return new ValidationEngine(
        new ValidationPipeline(
            new StringValidationStrategy()))
        .Execute(input);
}
