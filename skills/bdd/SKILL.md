---
name: bdd
description: Apply BDD/TDD methodology for writing tests and specifications. Use when writing tests, Gherkin scenarios, feature files, acceptance criteria, discussing test strategy, mocking patterns, or when the user mentions BDD, TDD, Given-When-Then, pytest, unittest, test coverage, or test-first development.
---

# Behavior-Driven Development (BDD)

## Core Philosophy

BDD bridges business and technical teams through:
- **Concrete examples** that describe system behavior
- **Ubiquitous language** shared by all stakeholders
- **Living documentation** that serves as executable tests

### Discovery → Development → Delivery

1. **Discovery**: Collaborate to understand requirements (Three Amigos, Example Mapping)
2. **Development**: Implement guided by examples (outside-in)
3. **Delivery**: Validate against real behavior

## Writing Workflow

### 1. Clarification First

If requirements are unclear, ambiguous, or too broad:
- Ask **1-2 critical questions** via AskQuestion tool
- Format: numbered list, ≤200 chars each
- Options: lettered sublist (first = default assumption)
- Only pre-read if ≤5 files will answer questions

### 2. Proportionality

Match spec complexity to feature complexity:
- Simple feature → 1-3 scenarios
- Complex feature → comprehensive coverage with edge cases
- Don't over-specify obvious behavior

### 3. Output Quality

- **No emojis** in feature files (plain text only)
- **Declarative language** — what, not how
- **Iterate** with user until scenarios are complete

## Given-When-Then Structure

```gherkin
Feature: [Business capability]
  As a [role]
  I want [feature]
  So that [benefit]

  Scenario: [Specific behavior description]
    Given [initial context/preconditions]
    When [action/event]
    Then [expected outcome]
```

### Writing Good Steps

| Step | Purpose | Examples |
|------|---------|----------|
| **Given** | Setup known state (past tense) | "a registered user exists", "my cart contains 3 items" |
| **When** | Single action/event (present) | "I submit the form", "the order is placed" |
| **Then** | Observable outcome (assertion) | "I should see confirmation", "email is sent" |
| **And/But** | Chain related steps | Continue Given/When/Then naturally |

## Scenario Best Practices

### DO
- Test **one behavior** per scenario
- Use **declarative** language (what, not how)
- Write from **user's perspective**
- Keep scenarios **3-7 steps**
- Include **edge cases** systematically

### DON'T
- Reference UI implementation details
- Include multiple behaviors in one scenario
- Use technical jargon
- Write scenarios after implementation

### Good vs Bad Examples

```gherkin
# ❌ Bad - Too technical, implementation-focused
Scenario: Login
  Given I click the login button
  And I type "user@example.com" in the email input
  And I type "password123" in the password field
  When I click submit
  Then the JavaScript redirects to dashboard.html

# ✅ Good - Declarative, business-focused
Scenario: Successful login with valid credentials
  Given a registered user with email "user@example.com"
  When the user logs in with correct password
  Then the user should see the dashboard
  And a session should be created
```

## Advanced Patterns

### Background (shared setup)

```gherkin
Feature: Blog posting

  Background:
    Given I am logged in as a blog owner
    And my blog "Tech Thoughts" exists

  Scenario: Create new post
    When I create a post titled "BDD Guide"
    Then the post should appear on my blog
```

### Scenario Outline (data-driven)

```gherkin
Scenario Outline: Password validation
  When I enter password "<password>"
  Then I should see "<message>"

  Examples:
    | password    | message                   |
    | abc         | Password too short        |
    | abcdefgh    | Password needs a number   |
    | abcdefgh1   | Password accepted         |
```

### Rules (Gherkin 6+)

```gherkin
Feature: Order refunds

  Rule: Full refunds available within 30 days

    Scenario: Refund within window
      Given an order placed 15 days ago
      When customer requests refund
      Then full refund should be processed

    Scenario: Refund after window
      Given an order placed 45 days ago
      When customer requests refund
      Then refund should be denied
```

## Collaboration Practices

### Three Amigos Session

Bring together **Business**, **Development**, **Testing** perspectives:

```
Feature: Password Reset

Business: "Users need to reset passwords via email"
Developer: "How long should tokens be valid?"
Tester: "What if they request multiple resets?"

Outcomes → Concrete scenarios
```

### Example Mapping

Use colored cards to explore features:

| Color | Represents |
|-------|------------|
| 🟡 Yellow | User Story/Feature |
| 🔵 Blue | Rules (acceptance criteria) |
| 🟢 Green | Examples (scenarios) |
| 🔴 Red | Questions (uncertainties) |

## Tags and Organization

```gherkin
@authentication @critical
Feature: User Login

  @smoke
  Scenario: Basic login flow
    ...

  @security
  Scenario: Account lockout after failed attempts
    ...

  @wip
  Scenario: Two-factor authentication
    ...
```

## Anti-Patterns

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| Testing after implementation | Loses specification value | Write scenarios first |
| UI-coupled scenarios | Brittle, hard to maintain | Use business language |
| Vague language | Ambiguous requirements | Use concrete examples |
| Too many steps | Loses expressiveness | Extract to Background or split |
| No negative tests | Missing edge cases | Add failure scenarios systematically |

## Quick Reference

For detailed reference material:
- [Gherkin syntax reference](references/gherkin-reference.md)
- [Collaboration workshops](references/collaboration.md)
- [Framework examples](references/framework-examples.md)
- [Testing practices](references/testing-practices.md) - TDD, mocking, coverage, test categories
