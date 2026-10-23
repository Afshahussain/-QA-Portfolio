# Test Plan - Personal Finance Tracker

**Prepared by:** Afsha Hussain
**Application:** Personal Finance Tracker (React.js + Django REST Framework + SQLite)
**Test type:** Manual functional testing
**Date:** May 2026

## 1. Objective

To verify that the Personal Finance Tracker works correctly for everyday use: signing in, recording income and expenses, organizing them into categories, setting budgets, viewing reports, and managing the user profile. The plan also checks that each user can only see their own data.

## 2. Scope

**In scope**

- Accounts: registration, login, and logout
- Dashboard
- Transactions page and Add Transaction form
- Categories
- Budget
- Reports
- Profile

**Out of scope**

- Performance and load testing
- Automated testing
- Cross-browser testing (only Google Chrome was used)
- Detailed backend API testing with Postman

## 3. Test Approach

Test cases were written by studying the Django models and the application screens, then executed manually on the running app. The following techniques were used:

- **Functional testing:** each feature works as expected with valid input
- **Boundary value analysis:** limits such as title length (200 characters), category name length (100 characters), and amount size (10 digits with 2 decimals)
- **Negative testing:** empty fields, invalid values, and duplicate entries
- **Data integrity testing:** for example, deleting a category also removes its related transactions and budgets
- **Security (user isolation):** one user must not see or change another user's data

## 4. Test Environment

| Item | Details |
|---|---|
| Frontend | http://localhost:5173 |
| Backend | http://127.0.0.1:8000 |
| Database | SQLite |
| Browser | Google Chrome 155.0.8059.85 (64-bit) |
| Operating system | Windows |

## 5. Test Deliverables

- Test cases: 80 across 8 modules (see Test_Cases/finance-tracker-test-cases.md)
- Execution summary with pass and fail counts
- Bug reports for every failed test case

## 6. Entry Criteria

- The frontend and backend both run locally without errors
- A test user account can be created and logged in
- Test cases are written and reviewed

## 7. Exit Criteria

- All 80 test cases have been executed or marked not applicable
- Every failed test case has a bug report
- Results are recorded in the execution summary

## 8. Risks and Assumptions

- Testing was done on a local setup, so results may differ on a deployed server
- Budget month is stored as free text with no validation, so invalid months are a known risk area
- Only one browser and one operating system were tested

## 9. Results Summary

| Metric | Count |
|---|---|
| Total test cases | 80 |
| Executed | 76 |
| Passed | 73 |
| Failed | 3 |
| Not applicable | 4 |
| Bugs reported | 2 |
