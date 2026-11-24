# Learn Jest with TypeScript – From Beginner to Advanced

Let's learn Jest properly, step by step, using real-world software testing examples and hands-on exercises.

I'll teach you as if you're preparing to work on a real automation testing project. We'll use TypeScript + Jest, and later integrate API testing, mocking, code coverage, JUnit reports, and CI/CD.

You don't just need to understand Jest syntax. You should be able to write test cases independently, debug failures, and build a complete automation framework.

## 1. What will you learn?

Jest learning roadmap

Beginner to advanced

1. Jest fundamentals
   Installation, configuration, test files, `describe`, `test`, `expect`.
   Start here
2. Assertions and matchers
   `toBe`, `toEqual`, `toContain`, `toThrow`, `toHaveLength`, etc.
3. Real-world test scenarios
   Login, registration, shopping cart, payment validation.
4. Setup and teardown
   `beforeEach`, `afterEach`, `beforeAll`, `afterAll`.
5. Asynchronous testing
   Promises, async/await, API calls, error handling.
6. Mocking and spying
   `jest.fn()`, `jest.mock()`, `jest.spyOn()`.
7. Advanced testing
   Parameterized tests, isolated tests, timers, modules.
8. API automation
   Test REST APIs, status codes, headers, response bodies.
9. Reports and coverage
   Jest coverage, JUnit XML, test execution reports.
10. Real-world automation framework
    Project structure, reusable utilities, environment configuration, CI/CD.

# Lesson 1: Understanding Jest fundamentals

## 2. What exactly happens when Jest runs?

Imagine you're testing an e-commerce application.

Your application has a login function:

ts

```ts
function login(username: string, password: string) {
    if (username === "admin" && password === "admin123") {
        return "Login successful";
    }

    return "Invalid credentials";
}
```

You want to verify:

1. Valid username and password should allow login.
2. Invalid password should reject login.
3. Empty username should not allow login.

Instead of manually testing these scenarios every time, Jest automates them.

The process looks like this:

Application code

login.ts

Jest test cases

login.test.ts

Execute application function

Pass input values to login()

Compare actual vs expected

Jest assertions

Test execution result

PASS or FAIL

## 3. Set up your first Jest + TypeScript project

First, make sure Node.js and npm are installed.

Check:

bash

```bash
node -v
npm -v
```

Create a project:

bash

```bash
mkdir jest-learning
cd jest-learning
npm init -y
```

Install dependencies:

bash

```bash
npm install --save-dev jest typescript ts-jest @types/jest
```

Initialize TypeScript:

bash

```bash
npx tsc --init
```

Configure Jest by creating `jest.config.ts`:

ts

```ts
import type { Config } from 'jest';

const config: Config = {
    preset: 'ts-jest',
    testEnvironment: 'node',
    testMatch: ['**/*.test.ts'],
};

export default config;
```

Add this script to `package.json`:

json

```json
{
  "scripts": {
    "test": "jest"
  }
}
```

Your initial folder structure:

text

```text
jest-learning/
│
├── src/
│   └── login.ts
│
├── tests/
│   └── login.test.ts
│
├── jest.config.ts
├── tsconfig.json
└── package.json
```

## 4. Write your first application function

Create `src/login.ts`:

ts

```ts
export function login(username: string, password: string): string {

    if (username === "admin" && password === "admin123") {
        return "Login successful";
    }

    return "Invalid credentials";
}
```

This is the actual application code. Jest will test this function.

## 5. Write your first Jest test cases

Create `tests/login.test.ts`:

ts

```ts
import { login } from "../src/login";

describe("Login functionality", () => {

    test("TC_001 - Login with valid credentials", () => {

        const result = login("admin", "admin123");

        expect(result).toBe("Login successful");

    });

    test("TC_002 - Login with invalid password", () => {

        const result = login("admin", "wrong123");

        expect(result).toBe("Invalid credentials");

    });

    test("TC_003 - Login with invalid username", () => {

        const result = login("user", "admin123");

        expect(result).toBe("Invalid credentials");

    });

});
```

Run your test cases:

bash

```bash
npm test
```

Expected output (simplified):

text

```text
PASS tests/login.test.ts

Login functionality
  ✓ TC_001 - Login with valid credentials
  ✓ TC_002 - Login with invalid password
  ✓ TC_003 - Login with invalid username

Test Suites: 1 passed, 1 total
Tests:       3 passed, 3 total
```

3 tests passed# 6. Understand the four most important Jest concepts

These four concepts are the foundation of almost every Jest test.

### A. `describe()` – Group test cases

Think of `describe()` as a test suite.

For example, in an e-commerce application:

ts

```ts
describe("Shopping Cart Tests", () => {

    // Add product test
    // Remove product test
    // Update quantity test
    // Calculate total test

});
```

You can have multiple `describe()` blocks:

ts

```ts
describe("Login Tests", () => {
    // Login scenarios
});

describe("Registration Tests", () => {
    // Registration scenarios
});

describe("Payment Tests", () => {
    // Payment scenarios
});
```

This helps organize hundreds of test cases.

### B. `test()` – Define an individual test case

`test()` defines what you want to verify.

ts

```ts
test("Verify user login", () => {

    // Test steps go here

});
```

You can also use `it()` instead of `test()`.

Both do the same thing.

ts

```ts
it("should allow valid login", () => {

});
```

### C. `expect()` – Define expected results

This is one of the most important Jest functions.

Suppose your application calculates the total price:

ts

```ts
const actualPrice = 100 + 200;

expect(actualPrice).toBe(300);
```

Here:

- `actualPrice` = Actual result.
- `300` = Expected result.
- `toBe()` = Assertion or matcher.

If actual and expected values match, the test passes.

If they don't match, the test fails.

### D. Matchers – Compare actual and expected values

Jest has many built-in matchers.

| Matcher | Purpose | Example |
| --- | --- | --- |
| `toBe()` | Exact primitive equality | `expect(5).toBe(5)` |
| `toEqual()` | Compare objects and arrays | `expect(a).toEqual(b)` |
| `toBeTruthy()` | Check truthy value | `expect(true).toBeTruthy()` |
| `toBeFalsy()` | Check falsy value | `expect(false).toBeFalsy()` |
| `toContain()` | Check array/string contains value | `expect(arr).toContain(10)` |
| `toHaveLength()` | Check length | `expect(arr).toHaveLength(3)` |
| `toBeNull()` | Check null | `expect(value).toBeNull()` |
| `toThrow()` | Check thrown errors | `expect(fn).toThrow()` |

Let's understand some of these with real-world examples.

# 7. Real-world example: E-commerce shopping cart

Imagine an online shopping application.

A customer:

1. Adds a product to the cart.
2. Updates the quantity.
3. Calculates the total amount.
4. Removes the product.

Let's implement some of that functionality.

Create `src/cart.ts`:

ts

```ts
export function calculateTotal(price: number, quantity: number): number {

    return price * quantity;

}

export function applyDiscount(total: number, discount: number): number {

    return total - (total * discount / 100);

}

export function isInStock(stock: number): boolean {

    return stock > 0;

}
```

Now write tests.

Create `tests/cart.test.ts`:

ts

```ts
import {
    calculateTotal,
    applyDiscount,
    isInStock
} from "../src/cart";

describe("Shopping Cart Tests", () => {

    test("TC_001 - Verify product total price", () => {

        const actual = calculateTotal(500, 2);

        expect(actual).toBe(1000);

    });

    test("TC_002 - Verify discount calculation", () => {

        const actual = applyDiscount(1000, 10);

        expect(actual).toBe(900);

    });

    test("TC_003 - Verify product is in stock", () => {

        const actual = isInStock(10);

        expect(actual).toBe(true);

    });

    test("TC_004 - Verify product is out of stock", () => {

        const actual = isInStock(0);

        expect(actual).toBe(false);

    });

});
```

Run:

bash

```bash
npm test
```

### Think like a real QA engineer

Would you test only the positive scenarios?

No. You should also think about:

- What happens if quantity is zero?
- What happens if quantity is negative?
- What happens if the discount is 100%?
- What happens if the discount is greater than 100%?
- What happens if product price is negative?
- What happens if stock is negative?

These are called edge cases and boundary-value scenarios.

A good automation engineer tests both expected behavior and unexpected inputs.

# 8. Your first hands-on exercises

Now I want you to write some test cases yourself.

These exercises are based on scenarios you might encounter in a real automation project.

Exercise 1 – Login functionality

Beginner

Write Jest test cases for the following scenarios:

| Test case | Input | Expected result |
| --- | --- | --- |
| Valid login | admin / admin123 | Success |
| Invalid password | admin / 123 | Failure |
| Invalid username | user / admin123 | Failure |
| Empty username | empty / admin123 | Failure |
| Empty password | admin / empty | Failure |

Your task: Write five `test()` blocks inside a `describe()` block.

Exercise 2 – Shopping cart

Beginner

Write test cases for:

- Product price = 200, quantity = 5. Expected total = 1000.
- Product price = 100, quantity = 3. Expected total = 300.
- Product price = 500, quantity = 0. Expected total = 0.
- Product price = 1000, discount = 20%. Expected total = 800.

Your task: Use `expect()` and `toBe()`.

Exercise 3 – Product validation

Intermediate

Write a function:

ts

```ts
function validateProduct(name: string): boolean
```

Requirements:

- Product name should not be empty.
- Product name should not contain only spaces.
- Valid product names should return `true`.
- Invalid product names should return `false`.

Your task: Write at least four Jest test cases.

## 9. Important Jest commands

These commands will be useful in your daily automation work.

| Command | Usage |
| --- | --- |
| `npm test` | Run all tests |
| `npx jest login.test.ts` | Run a specific test file |
| `npx jest --watch` | Watch for code changes |
| `npx jest --verbose` | Show detailed test results |
| `npx jest --coverage` | Generate code coverage |
| `npx jest -t "valid login"` | Run tests matching a name |

## 10. What we will learn next

Once you understand the fundamentals, we'll move into more powerful Jest concepts.

| Lesson | Topic | Real-world example |
| --- | --- | --- |
| 2 | Jest matchers in depth | Validate API responses |
| 3 | Setup and teardown | Database and test data preparation |
| 4 | Async/await | Test asynchronous API calls |
| 5 | Mock functions | Mock payment gateway |
| 6 | Spying | Track function calls |
| 7 | Parameterized tests | Test 50 login combinations |
| 8 | Exception handling | Invalid payment scenarios |
| 9 | API testing | GET, POST, PUT, DELETE |
| 10 | JUnit reporting | Generate XML reports |
| 11 | Code coverage | Measure tested code |
| 12 | CI/CD | Run Jest through Jenkins |

## Let's check your understanding

Before moving to Lesson 2, try these three questions.

Jest fundamentals quiz

0/3 answered

1. What is the purpose of describe() in Jest?

Execute application code

Group related test cases

Generate JUnit reports

2. What does expect(10).toBe(20) produce?

PASS

FAIL

Skipped

3. Which matcher should you use to compare two objects?

toBe()

toEqual()

toContain()

 Submit answers

My recommendation: Don't rush through all the topics. First complete Exercises 1–3 and understand how `describe`, `test`, `expect`, and matchers work. Then we'll build a proper Jest automation framework together, one concept at a time.