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
