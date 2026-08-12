---
type: llm
weight: 1
---

**Does the answer notice that `.row` is shared by every table in the app?**

The user said so in the prompt. Changing the grid template or the status rule reaches every table, not just the one with the problem.

PASS if the answer says this out loud and does something about it — scoping the change to the tables that need it, or explicitly warning that the fourth column changes everywhere.

FAIL if it edits the shared rule as though only one table existed.

Answer PASS or FAIL.
