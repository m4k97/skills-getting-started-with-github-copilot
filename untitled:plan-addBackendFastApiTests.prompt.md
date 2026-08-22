## Plan: Add FastAPI Backend Tests

Add a separate `tests/` suite using pytest and FastAPI's `TestClient`, structured with Arrange-Act-Assert. Keep production code unchanged unless test setup exposes a required compatibility issue.

**Steps**
1. Use `pytest` as the test framework and add `pytest` to `requirements.txt` while retaining existing runtime dependencies.
2. Create `tests/test_app.py` importing `app` and `activities` from `src.app`, with a client fixture and an isolation fixture that snapshots/restores the mutable in-memory activity data around each test.
3. Structure every test with clearly separated AAA sections: arrange request data and expected state, act through `TestClient`, then assert status, response JSON, redirect headers, and state changes.
4. Cover the root redirect to `/static/index.html` and `GET /activities` response shape/content.
5. Cover signup behavior: successful registration appends the email and returns the success message; unknown activities return 404; duplicate emails return 400 and do not add another participant.
6. Cover unregister behavior: successful deletion removes the email and returns the success message; unknown activities and missing participants return 404.
7. Run the full pytest suite and `git diff --check`; keep any unrelated existing changes untouched.

**Relevant files**
- `/workspaces/skills-getting-started-with-github-copilot/src/app.py` — API under test; reuse current route contracts and in-memory state.
- `/workspaces/skills-getting-started-with-github-copilot/requirements.txt` — add the pytest test dependency.
- `/workspaces/skills-getting-started-with-github-copilot/pytest.ini` — existing root Python path configuration supports imports from `src`.
- `/workspaces/skills-getting-started-with-github-copilot/tests/test_app.py` — new AAA-style HTTP tests and state-isolation fixture.

**Verification**
1. Install dependencies with `pip install -r requirements.txt` if needed.
2. Run `pytest -q` from the repository root and require all tests to pass.
3. Run `git diff --check`.

**Decisions**
- Test the behavior currently implemented, including duplicate prevention and unregistering; do not add a capacity test because the API does not currently enforce `max_participants` and implementing that rule is outside this testing request.
- Use API-level `TestClient` tests rather than direct function calls so route paths, query parameters, status codes, and JSON responses are verified.
- Restore the global `activities` state after each test to prevent test order dependence.
