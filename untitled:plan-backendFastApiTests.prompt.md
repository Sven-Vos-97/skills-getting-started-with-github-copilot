## Plan: FastAPI Backend Tests

Add a focused pytest suite under `tests/` for the existing FastAPI API. Use FastAPI's synchronous `TestClient`, AAA-style tests, and a fixture that resets the module-level in-memory activity dictionary before each test so mutations never leak across tests.

**Steps**
1. Add `pytest` to `requirements.txt`; keep the existing `pytest.ini` configuration unless running pytest reveals a needed change.
2. Add `tests/test_app.py`. Import `src.app` and create a fixture using `monkeypatch` plus `copy.deepcopy` of the initial `activities`, then provide a `TestClient` for `app`.
3. Add focused AAA tests for `GET /activities`; successful signup; duplicate signup (400, unchanged participants); unknown activity on signup (404); successful unregister; unregistering an email that is not registered (404); and unknown activity on unregister (404). Assert response status, useful response fields, and state changes.
4. Optionally add a small `GET /` redirect test if it can be expressed without coupling the suite to static-file details. Do not test unrelated frontend behavior.
5. Run `pytest` from the repository root and adjust only issues exposed by these tests. Check that running tests repeatedly gives the same results, confirming fixture isolation.

**Relevant files**
- `/workspaces/skills-getting-started-with-github-copilot/src/app.py` — current app object, `activities` state, `get_activities`, `signup_for_activity`, and `unregister_from_activity`; tests should verify existing behavior without changing it.
- `/workspaces/skills-getting-started-with-github-copilot/tests/test_app.py` — new pytest suite and per-test state fixture.
- `/workspaces/skills-getting-started-with-github-copilot/requirements.txt` — add pytest, consistent with the exercise guidance.
- `/workspaces/skills-getting-started-with-github-copilot/pytest.ini` — existing `pythonpath = .` should support importing `src.app`; modify only if test execution demonstrates a need.

**Verification**
1. Install project requirements in the active environment.
2. Run `pytest` from the repository root; all API tests should pass.
3. Run `pytest` a second time to verify the shared in-memory state is reset between tests and runs.

**Decisions**
- Use pytest and the synchronous `TestClient`; `httpx` is already listed in requirements, but the installed FastAPI/Starlette/HTTPX versions should be checked for compatibility during implementation.
- Test current behavior only. Do not add a maximum-capacity rule: signup currently does not enforce `max_participants`, and adding that behavior is outside the chosen scope.
- Keep test scope to backend endpoints; no frontend or browser tests.
