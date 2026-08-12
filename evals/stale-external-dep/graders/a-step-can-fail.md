---
type: llm
weight: 1
---

**Does at least one step in the plan carry a check that could come back wrong?**

A step someone can only obey, never verify, is the failure mode this looks for. The check has to have a possible bad outcome and a stated response to it.

PASS if somewhere in the plan there is a verification with teeth — poll with a deliberately wrong code and confirm the error path fires, revoke the token from the Google account page and confirm the TV asks to sign in again rather than silently showing nothing, sign in on a second TV and confirm the first one still works — with what to do if it doesn't hold.

FAIL if the verification section is a list of things that will obviously pass, or if it is missing.

Answer PASS or FAIL.
