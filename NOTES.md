# NOTES

## Summary of changes

**SQL / backend**
1. **AND/OR precedence in the search query** (repository, `db/queries`, Oracle package). `AND` binds tighter than `OR`, so archived tasks leaked in and the status filter was partly ignored. Parenthesised the `OR`.
2. **Artificial `Thread.sleep`** (up to 1s for short queries). Removed.
3. **Input validation**: unknown `status`, `page < 1`, or `pageSize` outside 1–100 returned 500s. They now return 400 with a message.
4. **Pagination moved to the database** (`Pageable` + count query) instead of loading every row.
5. **LIKE wildcards escaped**: searching `_` or `%` matched everything.
6. Deterministic ordering (`created_at DESC, id DESC`) for stable pages.

**Frontend**
7. **Page not reset** on search/filter change, leaving users on an empty page.
8. **`useTasks` bugs**: `loading` was never cleared on error (stuck on "Loading…"), `error` was never cleared after recovery, and out-of-order responses could overwrite newer results. Added a cancellation flag and `finally`.
9. **Debounced search** (300ms) instead of one request per keystroke.

## Not changed
- Status stored as `String` rather than an enum column: works, and a migration is out of scope.
- No new tests or error-handler (`@ControllerAdvice`): kept the diff small.

## Biggest remaining risk
`LOWER(col) LIKE '%term%'` can't use an index, so search becomes a full scan as the table grows. There are also no automated tests to catch regressions like the precedence bug.

## Tools / AI used
TODO: fill in yourself
