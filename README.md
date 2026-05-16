# QA Final Project - Playwright Test Framework

## Overview

An end-to-end test automation framework built with Playwright and TypeScript, targeting the [SauceDemo](https://www.saucedemo.com/) web application. The framework covers all core shopping features and is designed to run across multiple browsers with a shared authentication session.

## Contributors

- Hamza Zarour
- Hamza Naser

---

## Project Structure

```
qa-playwright-project/
├── playwright.config.ts          # Browser projects, baseURL, auth configuration
├── .env                          # Environment variables (credentials, base URL)
├── tsconfig.json
├── package.json
└── tests/
    ├── auth.setup.ts             # Runs once to save the login session
    ├── fixtures.ts               # Shared test setup
    ├── data/
    │   ├── users.ts              # User credentials loaded from .env
    │   └── products.ts           # Product names and prices
    ├── pages/                    # Page Object Models
    │   ├── LoginPage.ts
    │   ├── InventoryPage.ts
    │   ├── CartPage.ts
    │   └── CheckoutPage.ts
    ├── login/
    │   └── login.spec.ts
    ├── cart/
    │   ├── cart.spec.ts
    │   └── remove-from-cart.spec.ts
    ├── checkout/
    │   └── checkout.spec.ts
    ├── sort/
    │   └── sort.spec.ts
    └── users/
        └── users.spec.ts
```

---

## Features Tested

| Feature | File |
|---|---|
| Login (valid, invalid, locked out, empty fields) | `tests/login/login.spec.ts` |
| Add to cart (single and multiple items) | `tests/cart/cart.spec.ts` |
| Remove from cart (one item, multiple items) | `tests/cart/remove-from-cart.spec.ts` |
| Checkout flow (one item, multiple items, validation, cancel) | `tests/checkout/checkout.spec.ts` |
| Sort products (A-Z, Z-A, price low-high, price high-low) | `tests/sort/sort.spec.ts` |
| User type behavior and known bugs | `tests/users/users.spec.ts` |

---

## Playwright Concepts Used

| Concept | Implementation |
|---|---|
| Page Object Model (POM) | `tests/pages/` |
| Hooks | `test.beforeEach` in all spec files |
| Browser State Reuse | `auth.setup.ts` saves session, loaded via `storageState` |
| Multi-browser | Chromium and Firefox configured in `playwright.config.ts` |
| Grouping | `test.describe` in all spec files |
| Parameterized credentials | `data/users.ts` reads from `.env` |
| Projects configuration | 3 projects: setup, chromium, firefox |
| Annotations | `test.slow()` used for performance glitch user |

---

## Test Summary

| File | Tests |
|---|---|
| login.spec.ts | 5 |
| cart.spec.ts | 3 |
| remove-from-cart.spec.ts | 3 |
| checkout.spec.ts | 7 |
| sort.spec.ts | 4 |
| users.spec.ts | 10 |
| **Total** | **32 tests x 2 browsers = 64 runs** |

---

## User Types and Known Bugs

| User | Behavior |
|---|---|
| standard_user | Normal — everything works |
| locked_out_user | Cannot log in |
| problem_user | Broken images, broken add-to-cart buttons, broken last name field in checkout |
| performance_glitch_user | Login succeeds but takes more than 3 seconds |
| error_user | Login succeeds, Z-A sort does not work |
| visual_user | Login succeeds, has visual UI differences |

---

## Prerequisites

- Node.js 18 or later
- npm

---

## Installation

```bash
npm install
npx playwright install
```

---

## Environment Variables

Create a `.env` file in the project root:

```
BASE_URL=https://www.saucedemo.com
STANDARD_USER=standard_user
STANDARD_PASSWORD=secret_sauce
LOCKED_OUT_USER=locked_out_user
```

---

## Running Tests

Run all tests:
```bash
npx playwright test
```

Run a specific feature:
```bash
npx playwright test tests/login/
npx playwright test tests/cart/
npx playwright test tests/checkout/
npx playwright test tests/sort/
npx playwright test tests/users/
```

Run on a specific browser:
```bash
npx playwright test --project=chromium
npx playwright test --project=firefox
```

View the HTML report after a run:
```bash
npx playwright show-report
```

---

## How Authentication Works

`auth.setup.ts` runs before all other tests. It logs in once as `standard_user`, saves the browser session to `playwright/.auth/standard-user.json`, and all subsequent test browsers load that session automatically — no repeated login required.

Tests that require a fresh unauthenticated state (login tests, user type tests) override this with:

```ts
test.use({ storageState: { cookies: [], origins: [] } });
```
