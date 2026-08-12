---
type: llm
weight: 1
---

**Is every specific pixel value marked as an estimate rather than stated as fact?**

The correct width cannot be known from the source. It depends on whether the webfont actually loads, on the letter spacing, on the padding, and on whether a longer status word exists that the user never mentioned.

PASS if any concrete number the answer gives is presented as provisional — "roughly 104px, confirm against the real rendering", "my estimate is 112px", or similar. PASS also if the answer deliberately gives no fixed number and explains how to get the right one.

FAIL if a number is handed over as the answer with no signal that it was derived rather than measured. Showing the arithmetic behind the number does not by itself make it an estimate; the answer must say the result still needs confirming against the rendered page.

Answer PASS or FAIL.
