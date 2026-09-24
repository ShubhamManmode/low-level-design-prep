# YAGNI Principle

## 1. What is YAGNI?

**YAGNI = You Aren't Gonna Need It**

YAGNI is a software development principle that says:

> **Do not implement functionality until it is actually required.**

In simple words:

**Don't build something today just because you think you might need it in the future.**

Build what is required **now**, and add future functionality when there is a real requirement.

---

# 2. Why YAGNI is Important

Developers often think:

> "We might need this feature later, so let's build it now."

This can lead to:

- More code
- More complexity
- More bugs
- More testing
- More maintenance
- More development time
- More unnecessary infrastructure
- Difficult refactoring
- Increased cognitive load

YAGNI helps keep the system **simple and focused on current requirements**.

---

# 3. Simple Example

Suppose we need to build an API for creating users.

Current requirement:

```text
POST /users

Name
Email
Password

User
 ├── Name
 ├── Email
 ├── Password
 ├── Addresses
 ├── PhoneNumbers
 ├── SocialAccounts
 ├── Preferences
 ├── Localization
 ├── PaymentMethods
 └── NotificationSettings
