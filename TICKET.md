# Follow-up Ticket (Fill in)

**Title:**  
**Priority:** (P0/P1/P2/P3)  
**Owner:**  

## Description
What should be improved after the immediate incident is resolved?

## Acceptance criteria
- [ ] ...
- [ ] ...

## Notes / context
- Links to relevant code/areas
- Any monitoring/alerting suggestions

## Issue 1: Task fail with 500
• Title + description:
    -Null handling missing from “TaskEndpoints.cs” for ‘CreatedAt’ resulting in error 500 on application page
• Priority/severity and rationale
    -Severity 2 as it is across multiple users and not letting users to enter data
• Acceptance criteria
    -Reproduction steps no longer trigger the issue.
    -No regressions are introduced to existing functionality.
    -Appropriate logging/monitoring is added (if required).
    -Automated tests are added or updated.
    -Code passes CI/CD validation and code review.
• Notes/context (monitoring, tests, refactors, etc.)
    -Create a test which will confirm task creation returns 201 whether the timestamp header is valid, missing, empty, or invalid with invalid values falling back to server time. Blank userId/title now returns a 400 instead of a 500.
    -Please research if missing null handling was intentional and if the code changes need to be modified further.


## Issue 2: Task list is slow for some users
• Title + description:
    -Sorting is being done in memory instead of at DB level increasing the ‘Refresh’ time taken on application page
• Priority/severity and rationale
    -Severity 3 as it is across multiple users but is not hampering working of the application
• Acceptance criteria
    -Reproduction steps no longer trigger the issue.
    -No regressions are introduced to existing functionality.
    -Appropriate logging/monitoring is added (if required).
    -Automated tests are added or updated.
    -Code passes CI/CD validation and code review.
• Notes/context (monitoring, tests, refactors, etc.)
    -Tests confirm the DB-level rewrite didn't change behavior: results are filtered to the requested user, sorted newest first, and the limit is respected and capped at 200.
    -Monitoring can be applied at log level with a threshold value set 20 ms. If it is exceeded then we have to optimize the code further.


## Issue 3: Tasks appear duplicated or out of order after refresh
• Title + description:
    -Clicking on ‘Refresh’ button is adding duplicate entries on application page
• Priority/severity and rationale
    -Severity 3 as it is across multiple users but is not hampering working of the application
• Acceptance criteria
    -Reproduction steps no longer trigger the issue.
    -No regressions are introduced to existing functionality.
    -Appropriate logging/monitoring is added (if required).
    -Automated tests are added or updated.
    -Code passes CI/CD validation and code review.
• Notes/context (monitoring, tests, refactors, etc.)
    -The bug is in the frontend, so create an API test that confirms the backend never returns duplicate IDs, proving the fix belongs in main.js.
    -More log lines should be added for every data entry
    -Then log level monitoring can be added to catch duplicates


