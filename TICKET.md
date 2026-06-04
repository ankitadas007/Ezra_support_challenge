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

# Follow-up Tickets

## Issue 1: Task Creation Fails with HTTP 500

### Title

Add null handling for `CreatedAt` in `TaskEndpoints.cs`

### Description

Task creation can fail with an HTTP 500 error when the `CreatedAt` timestamp is missing, empty, or invalid. The endpoint should gracefully handle invalid timestamp values by falling back to server time instead of throwing an exception.

### Priority

**P1 (Severity 2)**

### Rationale

The issue impacts multiple users and prevents task creation, resulting in a loss of core application functionality.

### Acceptance Criteria

* [ ] Task creation succeeds when the timestamp header is valid.
* [ ] Task creation succeeds when the timestamp header is missing.
* [ ] Task creation succeeds when the timestamp header is empty.
* [ ] Task creation succeeds when the timestamp header contains an invalid value, using server time as a fallback.
* [ ] Blank or invalid `UserId` and `Title` values return HTTP 400 instead of HTTP 500.
* [ ] Reproduction steps no longer trigger the issue.
* [ ] No regressions are introduced to existing functionality.
* [ ] Appropriate automated tests are added or updated.
* [ ] Code passes CI/CD validation and code review.

### Notes / Context

* Add tests validating task creation behavior for valid, missing, empty, and invalid timestamp headers.
* Verify whether the absence of null handling was intentional and determine whether additional changes are required.
* Review logging to ensure malformed request data can be diagnosed effectively.

---

## Issue 2: Task List Performance Degradation

### Title

Move task sorting and limiting from application memory to the database layer

### Description

Task sorting is currently performed in memory after all records are retrieved from the database. This increases refresh times and unnecessarily consumes application resources.

### Priority

**P2 (Severity 3)**

### Rationale

The issue affects multiple users but does not prevent application usage.

### Acceptance Criteria

* [ ] Filtering, sorting, and limiting are performed at the database level.
* [ ] Results remain filtered to the requested user.
* [ ] Results are returned in descending order by creation date.
* [ ] The requested limit is respected and capped at 200 records.
* [ ] Reproduction steps no longer trigger the issue.
* [ ] No regressions are introduced to existing functionality.
* [ ] Appropriate automated tests are added or updated.
* [ ] Code passes CI/CD validation and code review.

### Notes / Context

* Add tests confirming that the database-level implementation preserves existing behavior.
* Add performance logging around task retrieval.
* Configure monitoring to flag requests exceeding a 20 ms retrieval threshold.
* Review query execution plans if performance targets are not met.

---

## Issue 3: Duplicate or Out-of-Order Tasks After Refresh

### Title

Prevent duplicate task entries from appearing after refresh

### Description

Clicking the **Refresh** button causes duplicate task entries to appear and may result in tasks being displayed out of order. Initial investigation suggests the issue originates in the frontend state management logic.

### Priority

**P2 (Severity 3)**

### Rationale

The issue affects multiple users and degrades the user experience but does not prevent application usage.

### Acceptance Criteria

* [ ] Refreshing the task list does not create duplicate entries.
* [ ] Task ordering remains consistent after refresh.
* [ ] Backend responses continue to return unique task IDs.
* [ ] Reproduction steps no longer trigger the issue.
* [ ] No regressions are introduced to existing functionality.
* [ ] Appropriate automated tests are added or updated.
* [ ] Code passes CI/CD validation and code review.

### Notes / Context

* Add an API test confirming the backend does not return duplicate task IDs.
* Investigate and correct frontend state management logic in `main.js`.
* Add diagnostic logging around refresh operations and state updates.
* Add monitoring to detect duplicate task IDs or abnormal refresh behavior.