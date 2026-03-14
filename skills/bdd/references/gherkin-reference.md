# Gherkin Syntax Reference

## Keywords

### Primary Keywords

| Keyword | Purpose | Followed by |
|---------|---------|-------------|
| `Feature` | High-level description, groups scenarios | `:` + title |
| `Rule` | Business rule grouping (Gherkin 6+) | `:` + title |
| `Scenario` / `Example` | Concrete example of behavior | `:` + title |
| `Given` | Initial context (precondition) | step text |
| `When` | Action or event | step text |
| `Then` | Expected outcome | step text |
| `And` / `But` | Continue previous step type | step text |
| `*` | Generic step (bullet-point style) | step text |
| `Background` | Shared setup for all scenarios | steps |
| `Scenario Outline` | Template for parameterized scenarios | steps with `<param>` |
| `Examples` | Data table for Scenario Outline | table |

### Secondary Keywords

| Keyword | Purpose |
|---------|---------|
| `"""` | Doc Strings (multiline text) |
| `\|` | Data Tables |
| `@` | Tags |
| `#` | Comments |

## Step Arguments

### Doc Strings

```gherkin
Given a blog post with Markdown body
  """markdown
  # My Post Title
  
  This is the content with **bold** text.
  """
```

Alternative delimiter (backticks):
```gherkin
Given a code snippet
  ```python
  def hello():
      print("Hello")
  ```
```

### Data Tables

```gherkin
Given the following users exist:
  | name   | email              | role    |
  | Alice  | alice@example.com  | admin   |
  | Bob    | bob@example.com    | user    |
```

**Escaping in tables:**
- Newline: `\n`
- Pipe: `\|`
- Backslash: `\\`

## Scenario Outline with Examples

```gherkin
Scenario Outline: Discount calculation
  Given a cart total of $<total>
  And customer has "<membership>" status
  When discount is applied
  Then final price should be $<final>

  Examples: Standard discounts
    | total | membership | final |
    | 100   | bronze     | 95    |
    | 100   | silver     | 90    |
    | 100   | gold       | 80    |

  Examples: Edge cases
    | total | membership | final |
    | 0     | gold       | 0     |
    | 100   | none       | 100   |
```

## Background Usage

```gherkin
Feature: Shopping cart

  Background:
    Given I am logged in as a customer
    And the product catalog is available

  Scenario: Add item to cart
    When I add "Blue Shirt" to my cart
    Then my cart should contain 1 item

  Scenario: Remove item from cart
    Given my cart contains "Blue Shirt"
    When I remove "Blue Shirt"
    Then my cart should be empty
```

**Background tips:**
- Keep short (≤4 lines)
- Use vivid, memorable names
- Move irrelevant details to higher-level steps

## Localization

Gherkin supports 70+ languages. Specify with header:

```gherkin
# language: es
Característica: Carrito de compras

  Escenario: Agregar producto
    Dado que estoy en la tienda
    Cuando agrego "Camiseta" al carrito
    Entonces el carrito tiene 1 producto
```

## File Organization

```
features/
├── authentication/
│   ├── login.feature
│   ├── logout.feature
│   └── password_reset.feature
├── shopping/
│   ├── cart.feature
│   └── checkout.feature
└── support/
    └── step_definitions/
```

## Tag Conventions

Tags are freeform - create any `@tag-name` your team needs. Common patterns:

| Category | Examples |
|----------|----------|
| Status | `@wip`, `@flaky`, `@quarantine` |
| Priority | `@critical`, `@smoke`, `@regression` |
| Execution | `@slow`, `@nightly`, `@ci-only` |
| Type | `@api`, `@ui`, `@manual` |
| Tracking | `@jira-1234`, `@sprint-42` |
| Domain | `@authentication`, `@payments` |
