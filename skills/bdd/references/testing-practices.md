# Testing Practices

## TDD Workflow

1. **Red**: Write failing test first
2. **Green**: Write minimal code to pass
3. **Refactor**: Clean up while tests pass

### BDD + TDD Together

- **BDD**: What should we build? (outside-in, acceptance tests)
- **TDD**: How should we build it? (inside-out, unit tests)

## Test Categories

| Type | Purpose | What to Mock | Speed |
|------|---------|--------------|-------|
| **Unit** | Test functions in isolation | All external dependencies | Fast |
| **Integration** | Test component interactions | External APIs only | Medium |
| **E2E/Acceptance** | Test full user flows | Nothing (real environment) | Slow |

## Mocking Philosophy

**Mock external I/O:**
- Network calls (APIs, databases)
- File system operations
- Time-dependent functions
- External services

**Never mock business logic:**
- Utility functions
- Domain calculations
- Validations and transformations
- Pure functions

## Test Naming

```python
# Pattern: action_condition_expected_result
def test_creates_user_when_valid_email():
    """Scenario: Create user with valid email"""
    # Given valid email
    # When create_user called
    # Then user is created

def test_rejects_user_when_duplicate_email():
    """Scenario: Reject duplicate email registration"""
    # Given existing user
    # When create_user with same email
    # Then raises DuplicateEmailError
```

## Coverage Targets

| Metric | Target | Notes |
|--------|--------|-------|
| Line coverage | 80%+ | Critical paths |
| Branch coverage | Both paths | if/else, try/except |
| Edge cases | Always | Empty, null, boundaries |

## Async Testing

```python
# pytest-bdd with async
@pytest.mark.asyncio
async def test_async_fetch():
    """Scenario: Async operation completes successfully"""
    # Given
    mock_client = AsyncMock()
    mock_client.fetch.return_value = {"data": "value"}
    
    # When
    result = await mock_client.fetch()
    
    # Then
    assert result == {"data": "value"}
```

## Anti-Patterns

| Anti-Pattern | Why It's Bad | Fix |
|--------------|--------------|-----|
| Mock business logic | Hides real bugs | Test real implementations |
| Test private methods | Couples to implementation | Test public interface |
| Magic values | Hard to understand | Use named constants/fixtures |
| Skip error cases | Missing coverage | Add negative test scenarios |
| Tests after code | Loses TDD benefits | Write tests first |
| Giant test functions | Hard to debug | One behavior per test |
| Flaky tests | Erode trust | Fix or quarantine immediately |
