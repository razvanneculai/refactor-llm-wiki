# Architecture & Decisions

A log of important choices and the reasoning behind them.

---

## [Decision title here]

**Date:** YYYY-MM-DD  
**Decision:** What was decided.  
**Why:** The reasoning, constraints, or tradeoffs that led here.  
**Alternatives considered:** What else was on the table.

---

## Example: Chose filesystem over database for storage

**Date:** 2024-03-15  
**Decision:** Store all data as markdown files, no database.  
**Why:** Keeps the project dependency-free, files are human-readable and easy to back up, and the data volume doesn't justify a DB.  
**Alternatives considered:** SQLite (overkill), JSON files (less readable for docs).
