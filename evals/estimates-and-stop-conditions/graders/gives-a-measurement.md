---
type: llm
weight: 1
---

**Does the answer tell the user how to measure the real value?**

PASS if it names something the user can actually do to find the true width — inspect the widest rendered pill, read `scrollWidth` off it in the console, temporarily set the track to `max-content` and look at the computed value, screenshot it. The technique doesn't matter; naming a measurement does.

FAIL if the answer only reasons about the value from font metrics and never tells the user how to check it against the running page.

Answer PASS or FAIL.
