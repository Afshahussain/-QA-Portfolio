# Personal Finance Tracker - Test Cases

**Application:** Personal Finance Tracker (React.js + Django REST Framework + SQLite)
**Tested by:** Afsha Hussain
**Test type:** Functional, Boundary, Negative, Data Integrity, Security (user isolation)
**Environment:** Local (Chrome, Windows) - update this to match yours

> **How to use this file:** The "Expected Result" column is based on the model definitions in the code. Run each test on the live app, then fill in **Actual Result** and **Status** (Pass / Fail). Any Fail becomes a bug report.

| Module | Test Cases | Count |
|--------|-----------|-------|
| Accounts (Authentication, Login and Logout) | TC_A01 - TC_A10 | 10 |
| Dashboard (Edge Cases) | TC_DB01 - TC_DB10 | 10 |
| Transactions Page (Edge Cases) | TC_TR01 - TC_TR10 | 10 |
| Add Transaction (Edge Cases) | TC_AT01 - TC_AT10 | 10 |
| Categories (Edge Cases) | TC_CT01 - TC_CT10 | 10 |
| Budget (Edge Cases) | TC_BG01 - TC_BG10 | 10 |
| Reports (Edge Cases) | TC_RP01 - TC_RP10 | 10 |
| Profile (Edge Cases) | TC_PR01 - TC_PR10 | 10 |
| **Total** | | **145** |

---

## 1. Login and Logout (Session Handling)

**These test cases focus on session behavior and edge cases beyond basic login and logout.

| ID | Scenario | Test Data | Expected Result | Actual Result | Status |
|----|----------|-----------|-----------------|-----------------|--------|
| TC_A01 | Access dashboard in a second tab while logged in on the first | Log in on Tab 1, open Tab 2, go directly to the dashboard URL | Dashboard loads in Tab 2 without asking to log in again, confirming the session is shared across tabs | As Expected | Passed |
| TC_A02 | Browser back button after logout | Log in, click logout, then press the browser's back button | Login page stays shown, dashboard does not reappear from cache | As Expected | Passed |
| TC_A03 | Same account logged in on two different browsers at once | Log in with the same account on Chrome and Edge at the same time | Check whether both sessions stay active, or whether the first one gets logged out | Stay active on both browsers | Passed|
| TC_A04 | Refresh the page while logged in | Log in, then press F5 on the dashboard | User stays logged in, dashboard reloads with data intact | As Expected | Passed |
| TC_A05 | Logout, then directly open a protected API URL | Log out, then open the transactions or dashboard API URL directly in the browser | Access denied or redirected to login, data is not shown |  As Expected | Passed |
| TC_A06 | Reuse an old session after logout | Log in, note the session or token if visible in dev tools, log out, then try reloading a protected page | Old session should not work, app should ask to log in again | As Expected | Passed |
| TC_A07 | Login with correct username but extra spaces | Username: " afsha ", correct password | Check whether the app trims spaces and logs in, or rejects it | the app trims spaces in username | Passed |
| TC_A08 | Rapid repeated login attempts with wrong password | Enter wrong password five or six times in a row quickly | Check whether the app has any lockout or rate limiting, or allows unlimited attempts | As Expected | Passed |
| TC_A09 | Close the browser without logging out, then reopen it | Log in, close the browser tab or window entirely, then reopen the app | Check whether the session is remembered or the user is asked to log in again | As Expected | Passed |
| TC_A10 | Logout from one tab while another tab is open and active | Log in, open the app in two tabs, click logout in Tab 1, then try an action in Tab 2 | Tab 2 should also lose access and be redirected to login once it tries an action | As Expected | Passed |

---

## 2. Dashboard Module (http://localhost:5173/dashboard)

| ID | Scenario | Test Data | Expected Result | Actual Result | Status |
|----|----------|-----------|-----------------|---------------|--------|
| TC_DB01 | Brand new user opens the dashboard before adding any data | 1. Register a new account.<br>2. Log in.<br>3. Open the Dashboard page without adding any category, transaction or budget. | Total income, total expense and balance all show 0.00. Charts show an empty-state message. No NaN, undefined, blank screen or error appears. |As Expected | Passed |
| TC_DB02 | Dashboard handles the maximum allowed amount | 1. Add an Income transaction of 99999999.99.<br>2. Add an Expense transaction of 0.01.<br>3. Open the Dashboard. | Income shows 99999999.99, expense shows 0.01, balance shows exactly 99999999.98. No scientific notation, no rounding error, and the cards do not overflow. | As Expected | Passed |
| TC_DB03 | Decimal addition accuracy | 1. Add two Expense transactions of 0.10 and 0.20 in the same month.<br>2. Open the Dashboard. | Total expense shows exactly 0.30, not 0.30000000000000004 or 0.3000. | As Expected | Passed |
| TC_DB04 | Expense is higher than income | 1. Add Income of 1000.<br>2. Add Expense of 2500.<br>3. Open the Dashboard. | Balance shows -1500.00 with a minus sign and is visually marked as negative. It is not shown as 0 and does not break the layout. | As Expected | Passed |
| TC_DB05 | Dashboard after editing a transaction amount | 1. Add an Expense of 500.<br>2. Note the dashboard expense total.<br>3. Edit the transaction and change the amount to 5000.<br>4. Return to the Dashboard. | Total expense increases by exactly 4500 and balance decreases by 4500 without needing to log out. | As Expected | Passed |
| TC_DB06 | Dashboard after deleting a category that has transactions | 1. Create category "Travel" and add two Expense transactions under it (300 and 700).<br>2. Note the total expense.<br>3. Delete the "Travel" category.<br>4. Open the Dashboard. | Total expense drops by 1000 because the linked transactions are deleted with the category. Charts no longer show "Travel". | As Expected | Passed |
| TC_DB07 | Data isolation between two users | 1. Log in as User A and add transactions totalling 5000.<br>2. Log out.<br>3. Log in as User B (a different account with no data).<br>4. Open the Dashboard. | User B sees zero totals and an empty chart. No figure or category from User A is visible. | As Expected | Passed |
| TC_DB08 | Category with the maximum name length in dashboard charts | 1. Create an Expense category with a 100-character name.<br>2. Add a transaction under it.<br>3. Open the Dashboard. | The category name is wrapped or truncated in the chart legend. Text does not overlap other elements and no horizontal scrollbar appears. | As Expected | Passed |
| TC_DB09 | Backend stops while the dashboard is open | 1. Open the Dashboard while the backend is running.<br>2. Stop the backend server.<br>3. Press F5 to refresh the Dashboard. | A clear error message such as "Unable to load data" is shown. The page does not stay blank or show an endless loading spinner. | As Expected | Passed |
| TC_DB10 | Dashboard on a mobile-sized screen | 1. Open the Dashboard.<br>2. Open browser developer tools and set the screen width to 375 px.<br>3. Scroll through the whole page. | Cards stack vertically, charts fit the screen width, text stays readable, and there is no horizontal scrolling. | As Expected | Passed |

---

## 3. Transactions Module

| ID | Scenario | Test Data | Expected Result | Actual Result | Status |
|----|----------|-----------|-----------------|---------------|--------|
| TC_TR01 | New user opens the transactions list with no data | 1. Log in with a new account that has no transactions.<br>2. Open the Transactions page. | The page shows a message such as "No transactions found". No empty table with broken headers or error appears. | As Expected | Passed |
| TC_TR02 | Large number of transactions | 1. Add 60 transactions with different titles and amounts.<br>2. Open the Transactions page.<br>3. Scroll or page through the full list. | All 60 transactions are reachable through scrolling or pagination. The page loads without freezing and no transaction is missing or duplicated. | As Expected | Passed |
| TC_TR03 | Several transactions on the same date | 1. Add three transactions with the same date but different titles.<br>2. Open the Transactions page.<br>3. Refresh the page twice. | All three appear in the same order after every refresh. None is hidden or repeated. | As Expected | Passed |
| TC_TR04 | Script injection in the title | 1. Add a transaction with title: `<script>alert('hacked')</script>`.<br>2. Open the Transactions page. | The title is displayed as plain text. No popup appears and no script runs. | As Expected | Passed |
| TC_TR05 | Hindi text and emoji in the title | 1. Add a transaction with title: "किराने का सामान 🛒".<br>2. Open the Transactions page. | The title is saved and displayed exactly as entered. No question marks or broken characters appear. | As Expected |  Passed |
| TC_TR06 | Delete the same transaction from two tabs | 1. Open the Transactions page in Tab 1 and Tab 2.<br>2. Delete transaction "Groceries" in Tab 1.<br>3. Without refreshing, delete the same transaction in Tab 2. | Tab 2 shows a friendly message such as "Transaction not found" and the list refreshes. The app does not crash or show a blank page. | As Expected | Passed |
| TC_TR07 | Conflicting edits from two tabs | 1. Open the same transaction in edit mode in two tabs.<br>2. In Tab 1, change the amount to 100 and save.<br>3. In Tab 2, change the amount to 200 and save.<br>4. Refresh the list. | The list shows one transaction with amount 200. No duplicate or corrupted record exists. | As Expected | Passed |
| TC_TR08 | Open the edit page of a deleted transaction | 1. Note the edit URL of a transaction.<br>2. Delete that transaction.<br>3. Paste the saved edit URL into the browser. | A "Not found" message is shown, or the user is redirected to the list. The page does not stay blank or show a raw server error. | As Expected | Passed |
| TC_TR09 | Access another user's transaction by changing the ID in the URL | 1. Log in as User A and note the ID of one of their transactions.<br>2. Log out and log in as User B.<br>3. Open the transaction URL or API URL using User A's ID. | Access is denied or a 404 is returned. User A's data is never displayed. | As Expected | Passed |
| TC_TR10 | Very long description | 1. Add a transaction with a description of 5000 characters.<br>2. Open the Transactions page and open that transaction. | The transaction saves. The description is wrapped or truncated, and the table layout does not break or stretch off-screen. | As Expected | Passed |

---

## 4. Add Transaction Module

| ID | Scenario | Test Data | Expected Result | Actual Result | Status |
|----|----------|-----------|-----------------|---------------|--------|
| TC_AT01 | Maximum allowed amount | 1. Open Add Transaction.<br>2. Title: "Max Test", Amount: 99999999.99, Payment mode: UPI, valid date and category.<br>3. Click Save. | The transaction is saved and appears in the list with amount 99999999.99. | As Expected | Passed |
| TC_AT02 | Amount above the maximum | 1. Open Add Transaction.<br>2. Enter Amount: 100000000.00 with all other fields valid.<br>3. Click Save. | A validation error is shown for the amount and the transaction is not saved. | As Expected | |
| TC_AT03 | Amount with three decimal places | 1. Open Add Transaction.<br>2. Enter Amount: 10.555 with all other fields valid.<br>3. Click Save. | A validation error such as "No more than 2 decimal places allowed" is shown. The transaction is not saved. | As Expected | Passed |
| TC_AT04 | Zero and negative amounts | 1. Open Add Transaction.<br>2. Enter Amount: 0 and click Save.<br>3. Enter Amount: -500 and click Save. | Both attempts are rejected with a message that the amount must be greater than zero. Nothing is saved. | As Expected | Passed |
| TC_AT05 | Title with only spaces | 1. Open Add Transaction.<br>2. Enter Title: "     " (five spaces) with all other fields valid.<br>3. Click Save. | A validation error says the title is required. The transaction is not saved. | As Expected | Passed |
| TC_AT06 | Title exactly at and above the 200-character limit | 1. Open Add Transaction.<br>2. Enter a 200-character title and save.<br>3. Add another transaction with a 201-character title and save. | The 200-character title is saved. The 201-character title is rejected with a length error. | As Expected | Passed |
| TC_AT07 | Leap year date validation | 1. Open Add Transaction.<br>2. Enter the date 29 Feb 2025 and save.<br>3. Enter the date 29 Feb 2028 and save. | 29 Feb 2025 is rejected as an invalid date. 29 Feb 2028 is saved successfully. | As Expected | Passed |
| TC_AT08 | Double-click on Save | 1. Fill the Add Transaction form with valid data.<br>2. Double-click the Save button quickly.<br>3. Open the Transactions page. | Only one transaction is created. No duplicate entry exists. | As Expected | Passed |
| TC_AT09 | Add a transaction when no category exists | 1. Log in with a new account that has no categories.<br>2. Open Add Transaction.<br>3. Fill in all fields and try to Save. | The category dropdown is empty and the user is told to create a category first. The form cannot be saved without a category. | As Expected | Passed |
| TC_AT10 | Tampered payment mode | 1. Open Add Transaction and fill in valid data.<br>2. In browser developer tools, change the payment mode value to "Cheque".<br>3. Click Save. | The server rejects the request with an error about an invalid payment mode. The transaction is not saved. | As Expected | Passed |

---

## 5. Categories Module

| ID | Scenario | Test Data | Expected Result | Actual Result | Status |
|----|----------|-----------|-----------------|---------------|--------|
| TC_CT01 | Category name with only spaces | 1. Open Categories.<br>2. Enter Name: "     " (five spaces), Type: Expense.<br>3. Click Add. | A validation error says the name is required. The category is not created. | As Expected | Passed |
| TC_CT02 | Name exactly at and above the 100-character limit | 1. Add a category with a 100-character name.<br>2. Add another category with a 101-character name. | The 100-character name is saved. The 101-character name is rejected with a length error. | As Expected | Passed |
| TC_CT03 | Same name with different letter case | 1. Add category "Food" (Expense).<br>2. Add another category "food" (Expense). | The second category is rejected as a duplicate of "Food". | As Expected | Passed |
| TC_CT04 | Same name with a different type | 1. Add category "Salary" with type Income.<br>2. Add category "Salary" with type Expense. | The second category is rejected with a message that the category name already exists. | As Expected | Passed |
| TC_CT05 | Name with extra spaces around it | 1. Add category "Food".<br>2. Add category "  Food  " (spaces before and after). | The second category is rejected as a duplicate because leading and trailing spaces are ignored. | As Expected | Passed |
| TC_CT06 | Special characters and HTML in the name | 1. Add a category named: `Gifts & <b>Party</b> 🎁`.<br>2. Open the Categories page and the Add Transaction dropdown. | The name is shown as plain text everywhere. The bold tag is not applied and nothing breaks the page. | As Expected | Passed |
| TC_CT07 | Delete a category that has transactions and a budget | 1. Create category "Rent" with two transactions and one budget.<br>2. Delete the "Rent" category.<br>3. Open Transactions, Budget and Dashboard. | A confirmation message appears before deletion. After deleting, the linked transactions and budget are gone and the Dashboard totals are updated. | As Expected | Passed |
| TC_CT08 | Change a category type after it has transactions | 1. Create Expense category "Misc" and add an Expense of 400 under it.<br>2. Edit "Misc" and change the type to Income.<br>3. Open the Dashboard. | The Dashboard totals are recalculated consistently with the new type. No transaction is lost or counted twice. | As Expected | Passed |
| TC_CT09 | Double-click on Add | 1. Enter a valid new category name.<br>2. Double-click the Add button quickly.<br>3. Review the category list. | Only one category is created. No duplicate appears. | As Expected | Passed |
| TC_CT10 | Access another user's category by ID | 1. Log in as User A and note a category ID.<br>2. Log out and log in as User B.<br>3. Open the category edit or delete URL using User A's ID. | Access is denied or a 404 is returned. User A's category cannot be viewed, edited or deleted. | As Expected | Passed |

---

## 6. Budget Module

| ID | Scenario | Test Data | Expected Result | Actual Result | Status |
|----|----------|-----------|-----------------|---------------|--------|
| TC_BG01 | Budget amount of zero | 1. Open Budget.<br>2. Select category "Food", Month: "October 2026", Amount: 0.<br>3. Click Save. | The budget is rejected with a message that the amount must be greater than zero. | As Expected | Passed |
| TC_BG02 | Negative budget amount | 1. Open Budget.<br>2. Select category "Food", Month: "October 2026", Amount: -1000.<br>3. Click Save. | The budget is rejected with a validation error. Nothing is saved. | As Expected | Passed |
| TC_BG03 | Maximum allowed budget amount | 1. Open Budget.<br>2. Select a category, Month: "October 2026", Amount: 99999999.99.<br>3. Click Save. | The budget is saved and displayed as 99999999.99 without the layout breaking. | As Expected | Passed |
| TC_BG04 | Month text longer than 20 characters | 1. Open Budget.<br>2. Enter Month: "October 2026 Festival Budget" (more than 20 characters).<br>3. Click Save. | The budget is rejected with a length error for the month field. | As Expected | Passed |
| TC_BG05 | Meaningless month value | 1. Open Budget.<br>2. Enter Month: "Octember 9999" with a valid category and amount.<br>3. Click Save. | The budget is rejected because the month is not a valid month. | As Expected | Passed |
| TC_BG06 | Duplicate budget for the same category and month | 1. Add a budget for "Food", October 2026, Amount 5000.<br>2. Add another budget for "Food", October 2026, Amount 7000. | The second budget is rejected with a message that a budget already exists for that category and month. | As Expected | Passed |
| TC_BG07 | Budget for an Income category | 1. Create category "Salary" with type Income.<br>2. Open Budget and select "Salary".<br>3. Enter Amount: 5000 and save. | The budget is rejected, or "Salary" is not offered in the list, because budgets apply only to Expense categories. | As Expected | Passed |
| TC_BG08 | Spending exceeds the budget | 1. Create a budget for "Food", October 2026, Amount 1000.<br>2. Add Expense transactions of 1500 in total under "Food" dated October 2026.<br>3. Open the Budget page. | The budget shows an over-budget status, with 150 percent used and 500 over. It is not hidden or shown as a normal state. | As Expected | Passed |
| TC_BG09 | Reduce a budget below what is already spent | 1. Have 800 spent in "Food" for October 2026 with a budget of 1000.<br>2. Edit the budget and change the amount to 500.<br>3. View the Budget page. | The remaining amount shows -300 and the budget is marked as exceeded. The page does not crash or show NaN. | As Expected | Passed |
| TC_BG10 | Delete the category that owns a budget | 1. Create a budget for category "Travel".<br>2. Delete the category "Travel".<br>3. Open the Budget page. | The budget for "Travel" is removed. No empty row or error appears. | As Expected | Passed |

---

## 7. Reports Module

| ID | Scenario | Test Data | Expected Result | Actual Result | Status |
|----|----------|-----------|-----------------|---------------|--------|
| TC_RP01 | Report for a user with no data | 1. Log in with a new account that has no transactions.<br>2. Open the Reports page. | The page shows "No data available" or a similar message. Charts are empty and no error appears. | As Expected | Passed |
| TC_RP02 | End date earlier than start date | 1. Open Reports.<br>2. Set Start Date: 31 Oct 2026 and End Date: 01 Oct 2026.<br>3. Apply the filter. | A validation message says the end date cannot be before the start date. No report is generated. | As Expected | Passed |
| TC_RP03 | Start date and end date are the same day | 1. Add transactions on 15 Oct 2026 and 16 Oct 2026.<br>2. Open Reports and set both dates to 15 Oct 2026.<br>3. Apply the filter. | Only transactions dated 15 Oct 2026 are included in the report. | As Expected | Passed |
| TC_RP04 | Date range with no transactions | 1. Open Reports.<br>2. Select a range in which no transaction exists, for example 01 Jan 2020 to 31 Jan 2020.<br>3. Apply the filter. | An empty-state message is shown with zero totals. The chart does not crash or show NaN. | As Expected | Passed |
| TC_RP05 | Report totals match the Dashboard and Transactions list | 1. Add 5 transactions in October 2026.<br>2. Open Reports for 01 Oct 2026 to 31 Oct 2026.<br>3. Compare totals with the Dashboard and the Transactions list. | Total income, total expense and balance are identical on all three pages. | As Expected | Passed |
| TC_RP06 | Transactions on the first and last day of the range | 1. Add a transaction on 01 Oct 2026 and another on 31 Oct 2026.<br>2. Open Reports and select 01 Oct 2026 to 31 Oct 2026. | Both boundary-date transactions are included in the report. | As Expected | Passed |
| TC_RP07 | Category breakdown percentages | 1. Add Expense transactions in four different categories.<br>2. Open Reports and view the category breakdown chart. | The percentages shown add up to 100 percent and each slice matches its category's share of the total. | As Expected | Passed |
| TC_RP08 | Report with a large amount of data | 1. Add 100 transactions across several months.<br>2. Open Reports and select the full date range. | The report loads within a few seconds without freezing the browser. Totals are correct. | As Expected | Passed |
| TC_RP09 | Change filters many times quickly | 1. Open Reports.<br>2. Change the date range or month 5 times in quick succession.<br>3. Wait for loading to finish. | The report displayed matches the last selected filter. Data from an earlier selection does not remain on screen. | As Expected |Passed |
| TC_RP10 | Deleted transaction disappears from the report | 1. Add an Expense of 2000 and open Reports to note the total.<br>2. Delete that transaction.<br>3. Reopen Reports. | The total expense drops by 2000 and the transaction no longer appears in any chart or table. | As Expected | Passed |

---

## 8. Profile Module 

| ID | Scenario | Test Data | Expected Result | Actual Result | Status |
|----|----------|-----------|-----------------|---------------|--------|
| TC_PR01 | Name with only spaces | 1. Open Profile.<br>2. Clear the name field and enter "     " (five spaces).<br>3. Click Save. | A validation error says the name is required. The old name remains unchanged. | As Expected | Passed |
| TC_PR02 | Invalid email formats | 1. Open Profile.<br>2. Enter email "abc@" and save.<br>3. Enter email "abc.com" and save.<br>4. Enter email "a b@gmail.com" and save. | All three attempts are rejected with an invalid email message. The saved email does not change. | As Expected | Passed |
| TC_PR03 | Email already used by another account | 1. Create two accounts, A (a@test.com) and B (b@test.com).<br>2. Log in as B and open Profile.<br>3. Change the email to a@test.com and save. | The update is rejected with a message that the email is already in use. | As Expected | Passed |
| TC_PR04 | Username already taken | 1. Log in as an account with username "user2".<br>2. Open Profile and change the username to an existing username, "user1".<br>3. Click Save. | The update is rejected with a message that the username already exists. | As Expected | Passed |
| TC_PR05 | Extremely long name | 1. Open Profile.<br>2. Enter a name of 300 characters.<br>3. Click Save. | The update is rejected with a length error, or the text is limited to the maximum allowed. The profile page layout does not break. | As Expected | Passed |
| TC_PR06 | Special characters and HTML in the name | 1. Open Profile.<br>2. Enter name: `<b>Afsha</b> <script>alert(1)</script>`.<br>3. Save and reload the page. | The name is displayed as plain text everywhere. No script runs and no formatting is applied. | As Expected | Passed |
| TC_PR07 | Change password with the wrong current password | 1. Open Profile and go to change password.<br>2. Enter an incorrect current password and a valid new password.<br>3. Click Save. | The change is rejected with a message that the current password is incorrect. The password stays the same. | As Expected | Passed |
| TC_PR08 | New password and confirm password do not match | 1. Open the change password section.<br>2. Enter the correct current password.<br>3. Enter new password "Test@1234" and confirm password "Test@4321".<br>4. Click Save. | The change is rejected with a message that the passwords do not match. | As Expected | Passed |
| TC_PR09 | Old password stops working after a change | 1. Change the password successfully from "OldPass@1" to "NewPass@1".<br>2. Log out.<br>3. Try logging in with "OldPass@1".<br>4. Try logging in with "NewPass@1". | Login with the old password fails. Login with the new password succeeds. | As Expected | Passed |
| TC_PR10 | Open the Profile page while logged out | 1. Log out of the app.<br>2. Paste http://localhost:5173/profile into the browser address bar. | The user is redirected to the login page. No profile data is displayed. | As Expected | Passed |

