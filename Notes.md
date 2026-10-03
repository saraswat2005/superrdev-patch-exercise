# Notes

## Summary of changes
- Fixed AND/OR precedence in the search query (JPA query, H2 reference SQL, Oracle package). Archived tasks were leaking into results and the status filter was ignored.
- Removed an artificial `Thread.sleep` in the controller that made short queries slower.
- Return 400 (not 500) for an invalid `status`, `page` or `pageSize`.
- Fixed stale responses overwriting newer results in `useTasks` (ignore flag in the effect cleanup).
- Reset to page 1 when the search or status filter changes.
- Fixed stuck "Loading..." and missing error state.
- Improvements: 300 ms search debounce, removed debug `console.log`, added an `id DESC` tie-breaker to the ordering.

## What I chose not to change
- Pagination in the database: it is a bigger change than a small patch should carry.
- Escaping `%` and `_` in the search term.
- Page validation in the Oracle procedure: I could not run it locally.

## Biggest remaining risk
All matching rows are loaded into memory and then sliced, with no index on `archived`, `status` or `created_at`. This will not scale as the table grows.

## Tools and AI used
I used Claude to help find and understand the bugs and to draft the patches. I pasted each change myself, ran the app and checked the API counts (8 and 31 results after the SQL fix). GitHub Copilot's agent also edited the two SQL reference files with the same bracket fix and generated its own NOTES.md, which I deleted and replaced with this one. The handwritten notes are my own explanations.

## Assumptions
- The app ran fine on Java 25, so I did not change the Java version.
- `pageSize` is capped at 100.
