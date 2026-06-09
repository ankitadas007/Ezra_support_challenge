# Runbook — SupportEngineerChallenge

> Update this file as part of the exercise.

## Service Overview

* **Service:** SupportEngineerChallenge.Api
* **Purpose:** Minimal task tracker (create and list tasks)
* **Data Store:** SQLite (`app.db` in the API working directory)

---

## Common Commands

### Run Locally

```bash
cd src/SupportEngineerChallenge.Api
dotnet run
```

### Run Tests

```bash
dotnet test
```

---

## Key Endpoints

* `GET /api/tasks?userId={id}&limit={n}`
* `POST /api/tasks`

---

## Using Log Artifacts

### Create Task 500 Errors

Inspect `artifacts/sample_api_log.txt` (or production logs).

Look for the `CreateTask request` log entry:

* `X-Client-Timestamp present=False`
* `length=0`

These values may indicate a missing or invalid timestamp header.

If present, review any associated exception details and stack traces to determine the source of the failure.

### Slow Task List Requests

Inspect `artifacts/sample_slow_list_log.txt` (or production logs).

Look for `ListTasks completed` entries with high `elapsedMs` values.

Correlate:

* `userId`
* `limit`
* request timestamp
* database activity

to identify performance bottlenecks.

---

## Troubleshooting Checklist

### Create Task Fails with HTTP 500

#### How to Diagnose

1. Review application logs for error messages and exceptions.
![image1](/artifacts/Screenshots/Task1/Screenshot%202026-06-08%20at%203.22.23 PM.png)
2. Search for `CreateTask request` entries around the time of the incident.
3. Verify whether the request contains:

   * Missing timestamp values
   * Invalid timestamp values
   * Missing required fields such as `UserId` or `Title`
4. Review stack traces to identify the exact code path causing the failure.
5. Check whether the API is returning HTTP 500 instead of an expected validation error (HTTP 400).

#### How to Verify the Fix

1. Reproduce the reported scenario in a test environment.
2. Test the following cases:

   * Valid timestamp
   * Missing timestamp
   * Empty timestamp
   * Invalid timestamp
3. Confirm task creation succeeds when expected and returns appropriate validation errors when input is invalid.
4. Verify no exceptions are generated in application logs.
5. Run automated tests and confirm all tests pass before deployment.

#### Mitigation / Rollback Plan

* Roll back to the last known good deployment if the fix introduces regressions.
* Monitor application logs after deployment for recurring task creation failures.
* If required, temporarily disable affected functionality until a corrected fix can be deployed.

---

### Task List Is Slow

#### How to Diagnose

1. Review logs for `ListTasks completed` entries.
![image1](/artifacts/Screenshots/Task2/Screenshot%202026-06-08%20at%204.56.38 PM.png)
2. Identify requests with unusually high `elapsedMs` values.
3. Correlate slow requests with:

   * User ID
   * Requested limit
   * Database load
4. Review query implementation to determine whether filtering, sorting, or limiting is being performed in memory instead of the database.
5. Compare performance against expected response time targets.

#### How to Verify the Fix

1. Reproduce the issue in a test environment.
2. Measure response times before and after the change.
3. Verify that:

   * Results are filtered correctly.
   * Results are sorted correctly.
   * Limits are respected.
4. Confirm application logs show improved response times.
5. Run automated tests and complete code review before deployment.

#### Mitigation / Rollback Plan

* Roll back to the previous deployment if performance degrades further.
* Monitor endpoint latency after deployment.
* Add additional logging and performance metrics if the root cause remains unclear.

---

### Duplicate Tasks or Incorrect Ordering After Refresh

#### How to Diagnose

1. Reproduce the issue by repeatedly clicking the Refresh button.
2. Inspect frontend code responsible for refreshing task data.
3. Review state management logic to determine whether task data is being appended instead of replaced.
4. Verify API responses contain unique task IDs and correctly ordered data.
5. Check browser developer tools and application logs for unexpected state updates.

#### How to Verify the Fix

1. Refresh the task list multiple times.
2. Confirm:

   * No duplicate tasks appear.
   * Task ordering remains consistent.
   * The displayed task count matches the API response.
3. Verify backend responses continue to return unique task IDs.
4. Run automated tests covering refresh behavior and state updates.
5. Validate the fix in a test environment before deployment.

#### Mitigation / Rollback Plan

* Roll back the frontend deployment if duplicate records reappear.
* Temporarily disable automatic refresh functionality if necessary.
* Add monitoring and logging around refresh operations to detect future regressions.

---

## Verification Steps

* Create tasks through both the UI and API.
* Refresh the task list repeatedly and confirm no duplicate records appear.
* Verify tasks are displayed in the correct order.
* Confirm the list endpoint returns only tasks belonging to the requested user.
* Validate handling of invalid, empty, and missing request values.

---

## Rollback / Mitigation Guidelines

* Roll back to the last known good deployment if a fix introduces regressions.
* Monitor logs closely after deployment.
* Add validation, error handling, and logging to reduce the likelihood of future incidents.
* Create follow-up tickets for any underlying design or process issues identified during incident resolution.
