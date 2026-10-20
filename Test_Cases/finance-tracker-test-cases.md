# Personal Finance Tracker - Test Cases

**Application:** Personal Finance Tracker (React.js + Django REST Framework + SQLite)
**Tested by:** Afsha Hussain
**Test type:** Functional, Boundary, Negative, Data Integrity, Security (user isolation)
**Environment:** Local (Chrome, Windows) - update this to match yours

> **How to use this file:** The "Expected Result" column is based on the model definitions in the code. Run each test on the live app, then fill in **Actual Result** and **Status** (Pass / Fail). Any Fail becomes a bug report.

| Module | Test Cases |
|--------|-----------|
| Accounts (Authentication) | TC_A01 - TC_A09 |
| Category | TC_C01 - TC_C13 |
| Transactions | TC_T01 - TC_T18 |
| Budget | TC_B01 - TC_B16 |
| Dashboard | TC_D01 - TC_D06 |

---

## 1. Category Module

**Model rules (from `category/models.py`):** `name` max 100 chars, `type` must be Income or Expense, the combination of user + name must be unique, deleting a category cascades to its transactions and budgets.

| ID | Scenario | Test Data | Expected Result | Actual Result | Status |
|----|----------|-----------|-----------------|---------------|--------|
| TC_C01 | Add Income category | Name: "Salary", Type: Income | Category saved and listed | | |
| TC_C02 | Add Expense category | Name: "Food", Type: Expense | Category saved and listed | | |
| TC_C03 | Blank name | Name empty | Validation error, not saved | | |
| TC_C04 | Name at max length | 100 characters | Saved successfully | | |
| TC_C05 | Name over max length | 101 characters | Validation error, not saved | | |
| TC_C06 | Invalid type | Type: "Savings" | Validation error (only Income / Expense allowed) | | |
| TC_C07 | Missing type | Type not selected | Validation error | | |
| TC_C08 | Duplicate name, same user | Add "Food" twice | Second one rejected (unique user + name) | | |
| TC_C09 | Same name, different user | User A and User B both add "Food" | Both allowed | | |
| TC_C10 | Same name, different type, same user | "Salary" as Income, then "Salary" as Expense | Rejected, because uniqueness is on name only. Confirm the error message is user-friendly | | |
| TC_C11 | Name differing only by case | "Food" then "food" | Check actual behavior and note whether it is treated as duplicate | | |
| TC_C12 | Whitespace-only name | Name: "   " | Should be rejected, log a bug if accepted | | |
| TC_C13 | Delete category that has transactions and budgets | Delete a category used in both | Linked transactions and budgets are also deleted (CASCADE). Check whether the user is warned first | | |

---

## 2. Transactions Module

**Model rules (from `transactions/models.py`):** `title` max 200, `amount` max 10 digits with 2 decimals, `payment_mode` one of Cash / UPI / Debit Card / Credit Card / Bank Transfer, `date` required, `description` optional.

| ID | Scenario | Test Data | Expected Result | Actual Result | Status |
|----|----------|-----------|-----------------|---------------|--------|
| TC_T01 | Add transaction with all valid fields | Title: "Groceries", Amount: 1500.50, Mode: UPI, valid date, valid category | Saved and shown in list | | |
| TC_T02 | Add without description | All valid, description blank | Saved (description is optional) | | |
| TC_T03 | Blank title | Title empty | Validation error | | |
| TC_T04 | Title at max length | 200 characters | Saved successfully | | |
| TC_T05 | Title over max length | 201 characters | Validation error | | |
| TC_T06 | Amount with 2 decimals | 99.99 | Saved as 99.99 | | |
| TC_T07 | Amount with 3 decimals | 10.555 | Validation error or rounding, note actual behavior | | |
| TC_T08 | Maximum amount | 99999999.99 | Saved successfully | | |
| TC_T09 | Amount above maximum | 100000000.00 | Validation error | | |
| TC_T10 | Zero or negative amount | 0 and -500 | Should be rejected. The model does not enforce this, so log a bug if accepted | | |
| TC_T11 | Non-numeric amount | "abc" | Validation error | | |
| TC_T12 | Each payment mode | Cash, UPI, Debit Card, Credit Card, Bank Transfer | All five accepted | | |
| TC_T13 | Invalid payment mode | "Cheque" | Validation error | | |
| TC_T14 | Missing date | Date empty | Validation error | | |
| TC_T15 | Invalid date | "31-02-2026" | Validation error | | |
| TC_T16 | Missing category | No category selected | Validation error | | |
| TC_T17 | Edit an existing transaction | Change amount and payment mode | Changes saved and reflected in list and dashboard | | |
| TC_T18 | Delete a transaction | Delete one transaction | Removed from list and dashboard totals update | | |

---

## 3. Budget Module

**Model rules (from `budget/models.py`):** `amount` max 10 digits with 2 decimals, `month` is a plain text field (max 20 chars) with no date validation, and there is no unique constraint on category + month.

| ID | Scenario | Test Data | Expected Result | Actual Result | Status |
|----|----------|-----------|-----------------|---------------|--------|
| TC_B01 | Add valid budget | Category: Food, Amount: 5000, Month: "October 2026" | Saved and listed | | |
| TC_B02 | Blank amount | Amount empty | Validation error | | |
| TC_B03 | Maximum amount | 99999999.99 | Saved successfully | | |
| TC_B04 | Amount above maximum | 100000000.00 | Validation error | | |
| TC_B05 | Zero or negative amount | 0 and -1000 | Should be rejected, log a bug if accepted | | |
| TC_B06 | Amount with 3 decimals | 500.555 | Validation error or rounding, note behavior | | |
| TC_B07 | Blank month | Month empty | Validation error | | |
| TC_B08 | Month at max length | 20 characters | Saved successfully | | |
| TC_B09 | Month over max length | 21 characters | Validation error | | |
| TC_B10 | Invalid month text | Month: "Octember" or "12345" | Model accepts any text, so check whether UI/API blocks it. Likely a bug if accepted | | |
| TC_B11 | Duplicate budget | Same category and same month added twice | No DB constraint, check whether duplicate is allowed. Likely a bug if accepted | | |
| TC_B12 | Budget for an Income category | Category type: Income | Check whether the app allows it (budgets normally apply to expenses) | | |
| TC_B13 | Missing category | No category selected | Validation error | | |
| TC_B14 | Edit a budget | Change amount | Updated amount saved | | |
| TC_B15 | Delete a budget | Delete one budget | Removed from list | | |
| TC_B16 | Spending exceeds budget | Budget 1000, add expenses totalling 1500 in the same category and month | Check whether the app warns or shows over-budget status | | |

---

## 4. Accounts (Authentication) Module

**Note:** The accounts app has no custom model, so it uses Django's built-in `User`. Expected results below are standard behavior. Confirm the exact fields on your register and login pages before testing.

| ID | Scenario | Test Data | Expected Result | Actual Result | Status |
|----|----------|-----------|-----------------|---------------|--------|
| TC_A01 | Register with valid details | Unique username, valid password | Account created | | |
| TC_A02 | Register with existing username | Username already registered | Error: username already exists | | |
| TC_A03 | Register with blank required fields | All fields empty | Validation errors shown | | |
| TC_A04 | Register with weak password | "123" | Rejected if password rules are enforced, otherwise note as observation | | |
| TC_A05 | Login with valid credentials | Registered user | Login successful, redirected to dashboard | | |
| TC_A06 | Login with wrong password | Valid username, wrong password | Error shown, no access | | |
| TC_A07 | Login with non-existent user | Unknown username | Error shown, no access | | |
| TC_A08 | Logout | Click logout | Session ended, protected pages no longer accessible | | |
| TC_A09 | Access protected page without login | Open dashboard URL directly while logged out | Redirected to login or access denied | | |

---

## 5. Dashboard Module

**Note:** The dashboard app has no model of its own. It likely summarizes transactions and budgets. Confirm what it displays (total income, total expense, balance) and adjust these cases.

| ID | Scenario | Test Data | Expected Result | Actual Result | Status |
|----|----------|-----------|-----------------|---------------|--------|
| TC_D01 | New user with no data | Fresh account | Dashboard loads with zero or empty state, no errors | | |
| TC_D02 | Totals match transactions | Add 2 income and 3 expense transactions | Total income, expense and balance calculated correctly | | |
| TC_D03 | Dashboard updates after add | Add a transaction | Totals update without logging out | | |
| TC_D04 | Dashboard updates after delete | Delete a transaction | Totals update correctly | | |
| TC_D05 | Decimal accuracy | Add 0.10 and 0.20 | Total shows 0.30 with no rounding error | | |
| TC_D06 | Data isolation | Log in as a different user | Only that user's data is shown | | |

---

## Cross-Module Security Tests (User Isolation)

| ID | Scenario | Expected Result | Actual Result | Status |
|----|----------|-----------------|---------------|--------|
| TC_S01 | User B opens User A's transaction by changing the ID in the API URL | Access denied or not found | | |
| TC_S02 | User B creates a transaction using User A's category ID | Rejected | | |
| TC_S03 | User B creates a budget using User A's category ID | Rejected | | |

---

## Bug Log

Record every failed test here.

| Bug ID | Test Case | Summary | Steps to Reproduce | Severity | Status |
|--------|-----------|---------|--------------------|----------|--------|
| BUG_01 | | | | | |
