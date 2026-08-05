# Module 3 Journal

## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/109

**Issue title:** Test coverage for `core/services/review_service.py` is below 40%

**Tier:** [x] Tier 2  [ ] Tier 1  [ ] Tier 3

**Problem summary:**
`core/services/review_service.py` orchestrates the entire review workflow —
creating a review, fetching it back while checking ownership, and listing a
user's reviews — but most of its code paths currently have no test coverage.
The existing test file only covers a handful of happy-path cases, so failure
handling (a review that never finishes generating, a partially-completed
review, a lookup for a review that doesn't belong to the requesting user) is
unverified even though this is described as the most critical service in the
application. A successful fix adds unit tests targeting the success,
partial-failure, and full-failure paths through `create_review`, `get_review`,
and `list_reviews`, using the existing mocked async DB session pattern already
present in `tests/unit/test_review_service.py`.

**Branch name:** test/109-review-service-test-coverage

**Setup confirmation:** [ ] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger

## Week 8 — Reproduction & solution planning

**Reproduction commit link:** https://github.com/darylcarter2006/pathreview/commit/9c83385

**Reproduction summary:**
Ran `pytest tests/unit/test_review_service.py --cov=core.services.review_service --cov-report=term-missing` in a local venv (no Docker needed — the tests mock the DB session). Observed actual coverage of 22% (worse than the ~40% the issue implies), with `process_review`, `_run_ingestion_pipeline`, and `_run_safety_checks` — the functions covering the success/partial-failure/full-failure workflow — at 0% coverage. Also discovered 13 of the 19 existing tests in that file are already failing from an unrelated `AsyncMock` misconfiguration bug (tracked separately as issue #158), which affects how I need to write new tests.

**PLAN.md link:** https://github.com/darylcarter2006/pathreview/blob/test/109-review-service-test-coverage/PLAN.md

**Walkthrough video (recommended):** (not recorded)

**Blockers or open questions:**
Docker/Node aren't installed on my machine yet, so I've only run the backend unit tests directly in a venv, not the full `make setup && make run` app — that's fine for this test-only issue since `process_review` and friends don't need the frontend or a live DB, but I still need real Docker/Node installed before Week 9 if I want to sanity-check the app end-to-end. Also watching issue #158 (claimed by other students) since it touches the same test file and could conflict with my new tests if their fix lands first.

## Week 9 — Solution building & PR submission

### Check-in 1 (mid-week)

**Current progress:**
Implemented all six sub-tasks from `PLAN.md`: added a `make_execute_result` helper that builds mocked `db.execute()` return values as plain `Mock()` objects (avoiding the `AsyncMock` auto-mocking trap behind issue #158), then added 17 new tests to `tests/unit/test_review_service.py` covering `process_review`'s success path, its two partial-failure branches (profile not found, safety checks failed), its full-failure branch (including the nested exception in the recovery block), `_run_ingestion_pipeline`'s per-source-type branches and error isolation, and `_run_safety_checks`'s four rejection conditions plus its own exception handler. Ran `pytest --cov=core.services.review_service --cov-report=term-missing`: coverage is now 95%, up from the 22% baseline. Also captured a `make check`/`make test-unit` baseline before touching any code and confirmed after my changes that the exact same 53 pre-existing test failures and the same categories of pre-existing lint/format/typecheck issues remain — my changes introduce no new failures (and net-fixed a few pre-existing lint issues in this file via `black`/`ruff --fix` while formatting my additions).

**Next steps:**
Open a draft PR against `ascherj/pathreview` for peer/mentor feedback, then address any feedback and mark it ready for review before the deadline.

**Blockers:**
None.

---

### Check-in 2 (end of week)

**PR link:** _pending — will add once opened_

**Branch:** test/109-review-service-test-coverage

**What you built:**
17 new unit tests for `core/services/review_service.py` covering `process_review`'s success/partial-failure/full-failure paths, `_run_ingestion_pipeline`'s per-source-type branches, and `_run_safety_checks`'s rejection branches — no production code changes, per the issue's scope.

**Tests added or updated:**
`tests/unit/test_review_service.py` — added a `make_execute_result` mock helper and 17 new test methods; raises module coverage from 22% to 95%. Did not modify the existing 19 tests (13 of which still fail due to the separately-tracked issue #158).

**Self-review confirmation:** [x] make check passes (no new failures vs. documented pre-existing baseline)  [x] make test-unit passes (same 53 pre-existing failures as baseline; all 17 new tests pass)

**Draft PR feedback received from:** none
