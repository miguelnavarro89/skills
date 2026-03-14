# BDD Collaboration Practices

## Three Amigos Sessions

### The Three Perspectives

| Role | Focus | Key Questions |
|------|-------|---------------|
| **Business** (PO/BA) | Value & rules | What problem? What value? What rules? |
| **Development** | Implementation | How to build? Constraints? Edge cases? |
| **Testing** | Quality & gaps | What could fail? What's missing? How to verify? |

### Session Format

**Duration:** 30-60 minutes per feature

**Process:**
1. Business presents user story (5 min)
2. Each role asks questions (15-20 min)
3. Collaboratively write scenarios (20-30 min)
4. Identify remaining questions/risks (5 min)

### Example Session

```
Feature: Password Reset

Business: "Users who forget passwords need reset via email."

Developer: "Token expiration time?"
Business: "1 hour."

Developer: "Rate limit reset requests?"
Business: "Good idea - max 3 per hour."

Tester: "Multiple reset requests - old tokens still valid?"
Business: "No, invalidate previous tokens."

Tester: "Non-existent email address?"
Business: "Same success message for security."

→ Outcomes become scenarios:
```

```gherkin
Scenario: Request password reset with valid email
  Given a user account exists for "user@example.com"
  When I request a password reset for "user@example.com"
  Then I should receive a reset email
  And the reset link should be valid for 1 hour

Scenario: Request password reset with non-existent email
  When I request a password reset for "unknown@example.com"
  Then I should see the same success message
  But no email should be sent

Scenario: Multiple password reset requests
  Given I have requested a password reset
  When I request another password reset
  Then the previous reset link should be invalidated
  And I should receive a new reset email

Scenario: Rate limiting password reset requests
  Given I have requested 3 password resets in the last hour
  When I request another password reset
  Then I should see "Too many requests, try again later"
```

## Example Mapping

### Card Colors

| Card | Represents | Quantity |
|------|------------|----------|
| 🟡 **Yellow** | User Story | 1 per session |
| 🔵 **Blue** | Rules/Criteria | Several |
| 🟢 **Green** | Examples | Multiple per rule |
| 🔴 **Red** | Questions | As needed |

### Workshop Format

**Duration:** 25-30 minutes per story

**Layout:**
```
       🟡 User Story
           │
    ┌──────┼──────┐
    │      │      │
   🔵     🔵     🔵    ← Rules
   │      │      │
  🟢🟢   🟢🟢🟢   🟢    ← Examples
  
  🔴 🔴 🔴              ← Questions (aside)
```

### Example: User Registration

**🟡 Story:** User registration

**🔵 Rules:**
- Email must be unique
- Password must be strong
- Age must be 18+

**🟢 Examples per rule:**

Email uniqueness:
- Register with new email → Success
- Register with existing email → Error

Password strength:
- "abc" → Too short
- "password" → Needs number
- "Password1!" → Accepted

Age verification:
- Age 25 → Allowed
- Age 17 → Blocked
- Age 18 → Allowed (boundary)

**🔴 Questions:**
- Do we verify email addresses?
- What defines "strong" password exactly?
- Parent consent for minors?

### When to Stop

Stop when:
- 🔴 Red cards outnumber 🟢 green cards → Need more discovery
- Too many 🔵 blue cards → Story too big, split it
- Clear examples for all rules → Ready to implement

## Specification by Example

### From Vague to Concrete

**Vague requirement:**
> "Users should be able to search for products."

**Concrete specification:**

```gherkin
Scenario: Search by product name
  Given products "Laptop", "Mouse", "Keyboard" exist
  When I search for "lap"
  Then I should see "Laptop" in results
  But I should not see "Mouse" or "Keyboard"

Scenario: Search with no results
  Given products "Laptop", "Mouse" exist
  When I search for "phone"
  Then I should see "No results found"

Scenario: Search is case-insensitive
  Given a product "Laptop" exists
  When I search for "LAPTOP"
  Then I should see "Laptop" in results
```

## Building Ubiquitous Language

### Discover Through Conversation

Ask stakeholders:
- "What do you call this?"
- "What's the difference between X and Y?"
- "When does this state change?"

### Document in Scenarios

```gherkin
# Use domain terms consistently
Given I am a Gold Member         # Not "User"
When I place an order            # Not "submit"
Then the order should be Pending # Not "in progress"
```

### Maintain a Glossary

```
Member: Customer with active subscription
Guest: Customer without subscription  
Order: Items ready for purchase
Cart: Temporary collection being considered
Subscription: Recurring payment arrangement
```

## Outside-In Development

Start from user-facing behavior, work inward:

```
1. Write failing scenario (acceptance test)
      ↓
2. Write failing unit test (current layer)
      ↓
3. Write minimal code to pass unit test
      ↓
4. Refactor
      ↓
5. Repeat until scenario passes
```

### Practical Flow

```
Scenario (Red) ──────────────────────┐
                                     │
    Controller Test (Red) ───────┐   │
                                 │   │
        Service Test (Red) ──┐   │   │
                             │   │   │
            Write Code       │   │   │
                             ↓   │   │
        Service Test (Green) ────│   │
                             ↓   │   │
    Controller Test (Green) ─────│   │
                             ↓       │
Scenario (Green) ────────────────────┘
```
