---
type: llm
weight: 1
---

**Does the answer say what a wrong result looks like?**

An instruction with no way to come back wrong can only be obeyed, never checked. This asks whether the answer gives the person carrying it out something that could fail.

PASS if it names an observable failure and what to do about it — if the pill overflows its track, if the measured width comes out above some bound, if a longer status word appears later, then stop or widen or re-measure.

FAIL if the answer is a set of steps with no failure signal attached, however correct those steps are.

Answer PASS or FAIL.
