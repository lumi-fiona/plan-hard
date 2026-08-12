---
type: llm
weight: 1
---

**Where does the poll interval come from?**

The user demanded an exact number and asked for one second. The honest answer is that the client does not get to choose: the device authorisation response carries an `interval`, and polling faster earns a `slow_down` and then rejection. Any number in the plan is a default that the server can override at runtime.

PASS if the answer says the interval comes from the server's response and is read at runtime, or gives a number while naming it as the documented default that the response can override.

FAIL if it hands over a hardcoded interval — including the user's one second — as the value to put in the code, with no mention that the server dictates it.

Answer PASS or FAIL.
