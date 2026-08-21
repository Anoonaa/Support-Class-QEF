---
theme: default
title: Module 2 - Chapter 2
---

# Authoring Tests in TypeScript
## Module 2, Chapter 2
### Locators, selectors, assertions, and fixtures

![Modern QA testing](https://images.unsplash.com/photo-1552664730-d307ca884978?auto=format&fit=crop&w=1400&q=80)

---

## Slide 1
# Learning outcomes

By the end of this chapter, engineers will be able to:
- explain why TypeScript matters in Playwright automation
- describe the difference between selectors and locators
- explain what assertions do in a test
- use locators for login, transfer, and dashboard flows
- recognise when fixtures reduce duplication and improve reliability
- understand how multiple tests build stronger automation coverage
- understand how strong test structure supports real fintech QA work

This chapter focuses only on Topic 2 and the practical skills required for modern automation.

---

## Slide 2
# Why this matters for Flash Group

Flash Group is a South African fintech business. It operates in a market where trust, speed, and accuracy matter. Customers expect digital services to work every time. That includes login screens, wallet pages, payments, onboarding, and account access.

Automation supports this by testing the same user journeys repeatedly. It helps the team detect defects before customers do. In a fintech environment, this matters because small errors can have large consequences.

A strong test suite helps teams to:
- validate login flows
- check transaction journeys
- confirm dashboard data
- catch broken UI logic early
- reduce manual effort in repetitive checks

This is why Playwright and TypeScript are so important in modern automation work.

---

## Slide 3
# Why TypeScript matters in automation

TypeScript gives structure to test code. Instead of writing loose JavaScript logic, engineers write code that is clearer and safer.

TypeScript helps with:
- catching mistakes earlier
- making code easier to read
- improving code reuse
- reducing guesswork during maintenance
- building large test suites without chaos

In a fintech app, test code often deals with forms, validations, account states, and transactions. TypeScript makes that more reliable.

This is not only about syntax. It is about writing automation that is easier to understand and easier to maintain.

---

## Slide 4
# A test is a behaviour check

A good Playwright test is not just a list of clicks. It is a clear statement of user behaviour and expected results.

A basic flow looks like this:
1. Go to the page
2. Find the element
3. Perform the action
4. Wait for the result
5. Assert the expected value

This pattern is simple, but it is powerful.

![User journey testing](https://images.unsplash.com/photo-1556157382-97eda2d62296?auto=format&fit=crop&w=1400&q=80)

---

## Slide 5
# What are selectors?

A selector is a way to tell the browser which element the test should interact with.

Selectors can target:
- a button
- a text box
- a link
- a form field
- a table cell
- a section of the page

Common selector types include:
- CSS selectors
- HTML IDs and classes
- text selector values
- role selectors
- XPath selectors

The goal is not to guess. The goal is to target the correct element with the least risk of breakage.

---

## Slide 6
# CSS selectors

CSS selectors are the most common way to find elements in web pages.

Example:
```ts
await page.click('#login-button');
await page.fill('.email-input', 'engineer@flashgroup.com');
```

This works well when a page has stable, meaningful selectors. It is fast and direct.

However, CSS selectors can be brittle. If a class name changes, or a layout is updated, the test may fail even when the user journey still works.

This is one reason why Playwright encourages locators for modern test authoring.

---

## Slide 7
# XPath selectors

XPath is another selector strategy. It allows the test to search the DOM by structure and path.

Example:
```ts
await page.locator('//button[contains(text(), "Login")]').click();
```

XPath is useful when the page structure is complex and CSS selectors are not enough.

But XPath is often slower to read and harder to maintain. It is not usually the first choice for modern Playwright tests. Use it only when it adds value.

A good rule is this:
- prefer semantic, stable locators
- use XPath only when necessary

---

## Slide 8
# What are locators?

Locators are Playwright’s main way to find and interact with elements.

A locator is more intelligent than a raw selector. It is built to work with:
- waiting for the correct element to appear
- retrying actions when the UI is still updating
- handling element visibility and state changes
- reducing flakiness during test execution

Example:
```ts
const loginButton = page.getByRole('button', { name: 'Log in' });
await loginButton.click();
```

This is stronger than a raw CSS selector because it matches the user-facing meaning of the element.

---

## Slide 9
# Recommended locator strategies

Playwright gives us many locator patterns. These are the most useful.

- `getByRole` for buttons, links, forms, and headings
- `getByLabel` for form fields with labels
- `getByText` for visible text in the page
- `getByPlaceholder` for input placeholders
- `getByTestId` for test-specific selectors, when needed

Example:
```ts
await page.getByLabel('Email').fill('engineer@flashgroup.com');
await page.getByLabel('Password').fill('SecurePass123!');
await page.getByRole('button', { name: 'Log in' }).click();
```

This matches how a real user interacts with the UI.

---

## Slide 10
# Real login flow in the Flash Group fintech app

A typical fintech login flow contains a few clear steps.

![Fintech login screen](https://images.unsplash.com/photo-1556740749-887f6717d7e4?auto=format&fit=crop&w=1400&q=80)

The user:
1. opens the login page
2. enters their email and password
3. clicks the login button
4. lands on the dashboard
5. sees their account overview

This is the foundation of many user journeys. A good automation test verifies the whole flow, not just the click.

---

## Slide 11
# Assertions in Playwright

An assertion is a test rule. It says, this thing must be true.

A test without assertions is only a sequence of actions. It does not prove success.

Example:
```ts
await expect(page).toHaveURL(/dashboard/);
await expect(page.getByText('Welcome back')).toBeVisible();
await expect(page.getByLabel('Amount')).toHaveValue('2500');
```

Assertions turn a script into a real test.

Without assertions, automation does not confirm that the product works.

---

## Slide 12
# Common assertion types

Playwright includes many assertion helpers. These are very useful in practical automation.

**URL assertions**
```ts
await expect(page).toHaveURL(/dashboard/);
```

**Visibility assertions**
```ts
await expect(page.getByText('Transaction successful')).toBeVisible();
```

**Value assertions**
```ts
await expect(page.getByLabel('Amount')).toHaveValue('2500');
```

**Text and content assertions**
```ts
await expect(page.getByRole('heading', { name: 'Dashboard' })).toBeVisible();
```

These assertions verify the outcome, not just the action.

---

## Slide 13
# Assertions in fintech testing

Fintech systems need strong assertions because the business outcome matters.

Examples include:
- after login, the user must land on the dashboard
- after transfer, the confirmation message must appear
- after a failed attempt, the error message must display
- after a limit check, the form must block the transaction

In real financial workflows, assertions protect against false positives.

A test should prove that the result is correct, not just that the button was clicked.

---

## Slide 14
# Fixtures in Playwright

Fixtures are reusable setup objects that eliminate repetition in test suites. They prepare preconditions (like login state, test data, or browser context) that multiple tests need.

**The fixture problem:**
Without fixtures, engineers repeat setup code in every test:
- login logic in 10 different test files
- same user credentials duplicated everywhere
- setup teardown scattered across the suite
- harder to maintain when the login process changes

**The fixture solution:**
Define setup once, reuse everywhere. This creates:
- cleaner, more readable tests
- faster maintenance when UI changes
- consistent test state across the suite
- reduced setup duplication
- more reliable, less flaky tests

In a fintech app, this matters because login, transfers, and dashboard workflows are used by many tests.

---

## Slide 15
# The Flash way: Page Objects with TypeScript Classes

In Flash automation, we use **Page Object Model (POM)** with TypeScript classes. This is the Flash way.

A page object is a class that wraps a single page and its interactions. It provides methods for the actions a user can take.

**Why this pattern matters:**
- separates page logic from test logic
- makes tests easier to read
- reduces duplication
- improves maintenance when UI changes
- makes large test suites scalable

Example - the LoginPage class:
```ts
import { Page } from '@playwright/test';

export class LoginPage {
  constructor(private page: Page) {}

  async open() {
    await this.page.goto('/login.html');
  }

  async login(email: string, password: string) {
    await this.page.getByLabel('Email').fill(email);
    await this.page.getByLabel('Password').fill(password);
    await this.page.getByRole('button', { name: 'Log in' }).click();
  }
}
```

Notice the constructor. It accepts the `page` object. This is the foundation of the Flash approach.

---

## Slide 16
# Using Page Objects in Tests

With a page object, tests become much clearer.

**Before (without page objects):**
```ts
test('engineer logs in', async ({ page }) => {
  await page.goto('/login.html');
  await page.getByLabel('Email').fill('engineer@flashgroup.com');
  await page.getByLabel('Password').fill('SecurePass123!');
  await page.getByRole('button', { name: 'Log in' }).click();
  await expect(page).toHaveURL(/dashboard/);
});
```

**After (with page objects - the Flash way):**
```ts
test('engineer logs in with page object', async ({ page }) => {
  const loginPage = new LoginPage(page);
  
  await loginPage.open();
  await loginPage.login('engineer@flashgroup.com', 'SecurePass123!');
  
  await expect(page).toHaveURL(/dashboard/);
});
```

The second version reads like a real user journey. The page object hides UI complexity.

---

## Slide 17
# Composing Multiple Page Objects

In real workflows, users move between pages. Page objects compose together cleanly.

Example - transfer workflow with multiple page objects:

```ts
import { Page } from '@playwright/test';
import { LoginPage } from '../pages/login-page';
import { TransferPage } from '../pages/transfer-page';

test('engineer logs in and transfers funds', async ({ page }) => {
  const loginPage = new LoginPage(page);
  const transferPage = new TransferPage(page);

  // Setup: login
  await loginPage.open();
  await loginPage.login('engineer@flashgroup.com', 'SecurePass123!');
  await expect(page).toHaveURL(/dashboard/);

  // Action: transfer
  await transferPage.open();
  await transferPage.completeTransfer('Apex Finance', '2500');

  // Assert: success
  await expect(page.getByText('Transfer successful')).toBeVisible();
});
```

This test reads like a business workflow. That's the power of good page objects.

---

## Slide 18
# Building the TransferPage Class

Here's what a more complex page object looks like:

```ts
import { Page } from '@playwright/test';

export class TransferPage {
  constructor(private page: Page) {}

  async open() {
    await this.page.goto('/transfer.html');
  }

  async completeTransfer(beneficiary: string, amount: string) {
    // Fill form
    await this.page.getByLabel('Beneficiary').selectOption(beneficiary);
    await this.page.getByLabel('Amount').fill(amount);
    
    // Review step
    await this.page.getByRole('button', { name: 'Review transfer' }).click();
    await this.page.getByText('Review your transfer').waitFor();
    
    // Confirm step
    await this.page.getByRole('button', { name: 'Confirm' }).click();
  }
}
```

The class encapsulates all the low-level locators and interactions. Tests don't need to know about the UI details.

---

## Slide 19
# Why Classes and Constructors Matter

TypeScript classes give us real benefits in test automation:

| Benefit | Explanation |
|---------|-------------|
| **Encapsulation** | UI details are hidden inside methods. Tests only call public methods. |
| **Reusability** | The same page object is used by many tests. Write once, use often. |
| **Type Safety** | Methods take typed parameters. TypeScript catches mistakes before runtime. |
| **Maintainability** | When the UI changes, update the page object once. All tests benefit. |
| **Readability** | Tests read like user journeys, not UI automation scripts. |
| **Scalability** | Suites with 100+ tests stay organized and readable. |

The constructor pattern (`constructor(private page: Page)`) is the key. It ties the page object to the actual browser page.

---

## Slide 20
# Advanced Fixtures: Using test.extend()

For complex suites, Playwright fixtures can be extended with custom setup logic.

```ts
import { test as base } from '@playwright/test';
import { LoginPage } from '../pages/login-page';

type TestFixtures = {
  loggedInPage: LoginPage;
};

export const test = base.extend<TestFixtures>({
  loggedInPage: async ({ page }, use) => {
    const loginPage = new LoginPage(page);
    
    await loginPage.open();
    await loginPage.login('engineer@flashgroup.com', 'SecurePass123!');
    
    // Use the fixture in the test
    await use(loginPage);
    
    // Cleanup after test (if needed)
    // await page.context().close();
  },
});
```

Now tests can use the loggedInPage fixture:
```ts
test('user can transfer funds', async ({ loggedInPage }) => {
  const transferPage = new TransferPage(loggedInPage.page);
  await transferPage.open();
  // ... rest of test
});
```

---

## Slide 21
# Data Builders for Test Data

Complex tests often need many variations of test data. Builders solve this cleanly.

A builder is a class that constructs test data:

```ts
export class TransferBuilder {
  private beneficiary = 'Apex Finance';
  private amount = '1000';
  private notes = '';

  withBeneficiary(ben: string): TransferBuilder {
    this.beneficiary = ben;
    return this;
  }

  withAmount(amt: string): TransferBuilder {
    this.amount = amt;
    return this;
  }

  withNotes(n: string): TransferBuilder {
    this.notes = n;
    return this;
  }

  build() {
    return {
      beneficiary: this.beneficiary,
      amount: this.amount,
      notes: this.notes,
    };
  }
}
```

Usage in tests:
```ts
test('large transfer requires review', async ({ loggedInPage }) => {
  const transfer = new TransferBuilder()
    .withAmount('50000')
    .withBeneficiary('External Bank')
    .build();

  // Use transfer data in test
});
```

This is much cleaner than passing many parameters or creating test data inline.

---

## Slide 22
# Fixture Composition and Reusability

Page objects and fixtures work together powerfully. A fixture can use page objects.

Example - a dashboard fixture:

```ts
export class DashboardFixture {
  constructor(
    public loginPage: LoginPage,
    public transferPage: TransferPage,
    public page: Page
  ) {}

  async loginAndNavigateToDashboard(
    email: string,
    password: string
  ) {
    await this.loginPage.open();
    await this.loginPage.login(email, password);
    // Now on dashboard
    return this;
  }

  async navigateToTransfer() {
    await this.transferPage.open();
    return this;
  }
}
```

This fixture composes multiple page objects and provides high-level business methods.

---

## Slide 23
# Fixtures and Setup Isolation

A good fixture ensures tests start in a clean, known state. This is critical for reliable automation.

Pattern:
```ts
test.beforeEach(async ({ page }) => {
  // Run before each test
  // Clear cookies, reset state, prepare data
  await page.context().clearCookies();
});

test.afterEach(async ({ page }) => {
  // Run after each test
  // Cleanup, reset app state, close contexts
  await page.context().close();
});
```

Why this matters:
- tests that depend on each other are fragile
- fixtures with proper setup/teardown are more stable
- isolation means one test failure doesn't cascade
- in fintech, this matters because money and accounts are at stake

Each test should be able to run independently, in any order.

---

## Slide 24
# Fixtures Summary: The Flash Way

Recap of fixture patterns in Flash automation:

| Pattern | Use Case |
|---------|----------|
| **Page Objects** | Wrap a single page and its interactions |
| **Fixtures (test.extend)** | Share complex setup across many tests |
| **Data Builders** | Create test data with fluent, readable syntax |
| **Composed Fixtures** | Combine multiple page objects for workflows |
| **Setup/Teardown** | Ensure tests start clean and isolated |

The Flash way combines these patterns:
1. Use page objects for UI interaction
2. Extend test with custom fixtures for complex setup
3. Build test data with clean, readable builders
4. Ensure isolation with beforeEach/afterEach
5. Let the test focus on business logic, not UI details

This creates a maintainable, scalable test suite.

---

## Slide 25
# Multiple tests build stronger coverage

A basic automation suite usually starts with a small set of tests that cover the main journey.

Examples include:
- login works
- the dashboard loads correctly
- the user can move to the transfer flow
- the transfer review matches the expected values

These tests build the foundation for broader coverage.

---

## Slide 26
# Why page objects matter in fintech

Page objects are crucial in fintech automation because:

**Complexity:** Fintech UIs have many forms, validations, and status states.

**Risk:** A small UI defect can block a user from accessing their account.

**Maintenance:** As the app evolves, selectors and flows change. Page objects centralize these changes.

**Clarity:** Tests should focus on the user's business goal (transfer money, check balance) not the UI details.

**Scale:** Fintech suites grow quickly. Page objects keep them organized.

For a company like Flash Group, this structure is not optional. It is essential.

---

## Slide 27
# DevTools and selector inspection

One of the best ways to author good locators is to use browser DevTools.

In the browser, engineers can:
- inspect the DOM
- find input names and labels
- review button roles and text
- check which selectors are stable
- compare CSS and XPath choices

This helps the team choose selectors that reflect the user experience and the actual UI structure.

![Browser devtools](https://images.unsplash.com/photo-1515879218367-8466d910aaa4?auto=format&fit=crop&w=1400&q=80)

---

## Slide 28
# Best practice summary

Use these ideas in your Playwright tests.

- Prefer locators over raw selectors
- Use `getByRole` and `getByLabel` when possible
- Keep tests focused on user behaviour
- Add assertions to prove outcomes
- Use page objects for UI encapsulation
- Extend fixtures for complex, repeated setup
- Build test data with clean, fluent builders
- Ensure test isolation with proper setup/teardown
- Validate the real business result

Good automation is simple, intentional, and easy to trust.

---

## Slide 29
# Final recap

Topic 2 is about how engineers actually write quality automation in TypeScript.

The main elements are:
- selectors and locators for element interaction
- assertions as proof of expected behaviour
- page objects for UI encapsulation
- fixtures for setup and state reuse
- data builders for test data
- test isolation for reliability
- multiple tests for real user journeys
- good structure for growing automation frameworks

This is the foundation for strong Playwright testing in a modern fintech environment.

---

## Slide 30
# Final takeaway

Playwright tests are strongest when they are clear, stable, and business-focused.

In a fintech company like Flash Group, this matters because customers depend on digital systems to work correctly and consistently.

A strong automation test suite should do three things well:
- find elements reliably with good locators
- perform correct actions with clear page objects
- prove expected results with strong assertions

Combine these with good fixtures and builders, and you have a professional, maintainable automation framework.

That is the heart of authoring tests in TypeScript for modern fintech QA.

![Modern team QA](https://images.unsplash.com/photo-1522202176988-66273c2fd55f?auto=format&fit=crop&w=1400&q=80)
