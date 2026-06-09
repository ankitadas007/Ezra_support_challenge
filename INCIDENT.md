# Incident Summary

## Issue 1: Task Creation Fails with HTTP 500

**Title:** Creating a task occasionally fails with an HTTP 500 error
**Date:** 03/06/2026
**Severity:** Sev 2 – Multiple users were affected and core functionality was unavailable.

### Impact

* Some users submitted invalid or incomplete data and received a generic HTTP 500 error instead of a clear validation message.
* Users were unable to create tasks in affected scenarios.
![image1](/artifacts/Screenshots/Task1/Screenshot%202026-06-08%20at%203.21.56 PM.png)

### Detection

* The issue was reported by users.

### Timeline

* Issue observed on Sunday, May 31, between approximately 16:00 PDT and 16:30 PDT.
* Emergency fix deployed at approximately 17:00 PDT.

### Root Cause

* The application did not properly handle missing or invalid timestamp values in “TaskEndpoints.cs” .
* A null or invalid timestamp could result in an unhandled exception, causing the API to return an HTTP 500 error.

![image2](/artifacts/Screenshots/Task1/Screenshot%202026-06-08%20at%203.22.35 PM.png)
![image2](/artifacts/Screenshots/Task1/Screenshot%202026-06-08%20at%203.22.35 PM.png)

### Mitigation / Resolution

* Added validation and null handling for the timestamp field.
* When a timestamp is missing, empty, or invalid, the application now falls back to the server's current time.

### Verification

* The fix was validated in the test environment.
* Regression testing confirmed task creation succeeds with valid, missing, empty, and invalid timestamps.
* The change was reviewed and deployed to production through the standard Git deployment process.

![image3](/artifacts/Screenshots/Task1/Screenshot%202026-06-08%20at%203.22.45 PM.png)
![image4](/artifacts/Screenshots/Task1/Screenshot%202026-06-08%20at%203.22.23 PM.png)

### Follow-up Actions

* Create a follow-up ticket to determine why defensive null handling was absent and identify whether additional validation improvements are required.
* Review API input validation patterns across similar endpoints.

---

## Issue 2: Task List Performance Degradation

**Title:** Task list refresh is slow for some users
**Date:** 03/06/2026
**Severity:** Sev 3 – Multiple users were affected, but the application remained functional.

### Impact

* Some users experienced slow page refreshes when retrieving task data.
* The issue negatively impacted user experience but did not prevent application use.
![image5](/artifacts/Screenshots/Task2/Screenshot%202026-06-08%20at%204.56.18 PM.png)
![image6](/artifacts/Screenshots/Task2/Screenshot%202026-06-08%20at%204.56.26 PM.png)

### Detection

* The issue was reported by users.

### Timeline

* Issue observed on Sunday, May 31, between approximately 16:00 PDT and 16:30 PDT.
* Emergency fix deployed at approximately 17:50 PDT.

### Root Cause

* Task sorting was performed in application memory after retrieving records from the database.
* This resulted in unnecessary processing and increased response times.
![image7](/artifacts/Screenshots/Task2/Screenshot%202026-06-08%20at%204.56.45 PM.png)

### Mitigation / Resolution

* Updated the query to perform filtering, sorting, and limiting directly at the database level.

![image8](/artifacts/Screenshots/Task2/Screenshot%202026-06-08%20at%204.56.54 PM.png)
![image9](/artifacts/Screenshots/Task2/Screenshot%202026-06-08%20at%204.57.03 PM.png)

### Verification

* The fix was validated in the test environment.
* Functional testing confirmed no change in returned results.
* Average refresh time decreased from over 150 ms to under 10 ms.
* The change was deployed to production through the standard Git deployment process.

### Follow-up Actions

* Review other endpoints for similar in-memory processing patterns.
* Add monitoring and performance thresholds to identify future query regressions.

---

## Issue 3: Tasks Appear Duplicated or Out of Order After Refresh

**Title:** Tasks appear duplicated or out of order after refresh
**Date:** 03/06/2026
**Severity:** Sev 3 – Multiple users were affected, but the application remained functional.

### Impact

* Some users observed duplicate task entries after repeatedly clicking the Refresh button.
* Task ordering could become inconsistent, leading to confusion.

### Detection

* The issue was reported by users.

### Timeline

* Issue observed on Sunday, May 31, between approximately 16:00 PDT and 16:30 PDT.
* Emergency fix deployed at approximately 18:50 PDT.

### Root Cause

* Frontend state management logic appended newly retrieved task data to the existing task collection instead of replacing or deduplicating records.
* This caused duplicate entries to accumulate after each refresh.

### Mitigation / Resolution

* Updated the frontend refresh logic to replace the existing task collection with the latest API response.
* Added safeguards to prevent duplicate task IDs from being displayed.

### Verification

* The fix was validated in the test environment.
* Repeated refresh testing confirmed that duplicate entries no longer appear.
* Task ordering remained consistent across refresh operations.
* The change was deployed to production through the standard Git deployment process.

### Follow-up Actions

* Create a follow-up ticket to review frontend state management practices.
* Add automated tests covering repeated refresh scenarios and duplicate detection.
* Consider adding client-side monitoring to detect duplicate rendering issues in the future.
