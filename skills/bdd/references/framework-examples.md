# BDD Framework Examples

## Python (pytest-bdd)

### Feature File

```gherkin
# features/auth/login.feature
Feature: User authentication

  Scenario: Successful login
    Given a registered user "alice@example.com"
    When the user logs in with correct password
    Then the user should be authenticated
    And a session token should be created
```

### Step Definitions

```python
# tests/step_defs/test_login.py
import pytest
from pytest_bdd import given, when, then, scenarios

scenarios('../features/auth/login.feature')

@given('a registered user "<email>"')
def registered_user(email, user_factory):
    return user_factory.create(email=email)

@when('the user logs in with correct password')
def login_user(registered_user, auth_service):
    return auth_service.login(registered_user.email, 'password')

@then('the user should be authenticated')
def verify_authenticated(login_result):
    assert login_result.success is True

@then('a session token should be created')
def verify_session_token(login_result):
    assert login_result.token is not None
```

### Conftest Fixtures

```python
# tests/conftest.py
import pytest

@pytest.fixture
def user_factory(db_session):
    class UserFactory:
        def create(self, email, password='password'):
            user = User(email=email)
            user.set_password(password)
            db_session.add(user)
            db_session.commit()
            return user
    return UserFactory()
```

## JavaScript (Cucumber.js)

### Feature File

```gherkin
# features/cart.feature
Feature: Shopping cart

  Scenario: Add item to cart
    Given I have an empty cart
    When I add "Blue Shirt" to my cart
    Then my cart should contain 1 item
    And the total should be $29.99
```

### Step Definitions

```javascript
// features/step_definitions/cart_steps.js
const { Given, When, Then } = require('@cucumber/cucumber');
const { expect } = require('chai');

Given('I have an empty cart', function() {
  this.cart = new Cart();
});

When('I add {string} to my cart', function(productName) {
  const product = this.catalog.find(productName);
  this.cart.add(product);
});

Then('my cart should contain {int} item(s)', function(count) {
  expect(this.cart.items.length).to.equal(count);
});

Then('the total should be ${float}', function(total) {
  expect(this.cart.total).to.equal(total);
});
```

### World Configuration

```javascript
// features/support/world.js
const { setWorldConstructor } = require('@cucumber/cucumber');

class CustomWorld {
  constructor() {
    this.cart = null;
    this.catalog = new ProductCatalog();
  }
}

setWorldConstructor(CustomWorld);
```

## Ruby (Cucumber)

### Feature File

```gherkin
# features/order.feature
Feature: Order placement

  Scenario: Place order with items in cart
    Given I have "Blue Shirt" in my cart
    When I place my order
    Then I should receive an order confirmation
    And the order status should be "pending"
```

### Step Definitions

```ruby
# features/step_definitions/order_steps.rb
Given('I have {string} in my cart') do |product_name|
  @cart = Cart.new
  product = Product.find_by(name: product_name)
  @cart.add(product)
end

When('I place my order') do
  @order = OrderService.place(@cart, @current_user)
end

Then('I should receive an order confirmation') do
  expect(@order.confirmation_number).not_to be_nil
end

Then('the order status should be {string}') do |status|
  expect(@order.status).to eq(status)
end
```

## Java (Cucumber-JVM)

### Feature File

```gherkin
# src/test/resources/features/payment.feature
Feature: Payment processing

  Scenario: Successful credit card payment
    Given a valid credit card ending in "4242"
    When I pay $50.00
    Then the payment should be approved
    And I should receive a receipt
```

### Step Definitions

```java
// src/test/java/steps/PaymentSteps.java
package steps;

import io.cucumber.java.en.*;
import static org.junit.Assert.*;

public class PaymentSteps {
    private CreditCard card;
    private PaymentResult result;

    @Given("a valid credit card ending in {string}")
    public void validCreditCard(String lastFour) {
        this.card = TestCards.validCardEndingIn(lastFour);
    }

    @When("I pay ${double}")
    public void processPayment(double amount) {
        PaymentService service = new PaymentService();
        this.result = service.charge(card, amount);
    }

    @Then("the payment should be approved")
    public void paymentApproved() {
        assertTrue(result.isApproved());
    }

    @Then("I should receive a receipt")
    public void receiptGenerated() {
        assertNotNull(result.getReceipt());
    }
}
```

## Common Patterns Across Frameworks

### Hooks (Before/After)

```python
# pytest-bdd
@pytest.fixture(autouse=True)
def setup_teardown(db):
    # Before
    db.begin_transaction()
    yield
    # After
    db.rollback()
```

```javascript
// Cucumber.js
const { Before, After } = require('@cucumber/cucumber');

Before(function() {
  this.db.beginTransaction();
});

After(function() {
  this.db.rollback();
});
```

### Tagged Hooks

```javascript
// Run only for @database tagged scenarios
Before({ tags: '@database' }, function() {
  this.db.seed();
});
```

### Data Tables

```gherkin
Given the following products exist:
  | name       | price | stock |
  | Blue Shirt | 29.99 | 100   |
  | Red Pants  | 49.99 | 50    |
```

```python
@given('the following products exist')
def create_products(datatable):
    for row in datatable:
        Product.create(
            name=row['name'],
            price=float(row['price']),
            stock=int(row['stock'])
        )
```

### Parameterized Steps

```gherkin
Scenario Outline: Discount tiers
  Given a cart total of $<total>
  When <membership> discount is applied
  Then final price should be $<final>

  Examples:
    | total | membership | final |
    | 100   | bronze     | 95    |
    | 100   | gold       | 80    |
```
