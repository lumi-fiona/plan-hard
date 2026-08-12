---
type: llm
weight: 1
---

**Does the answer offer a fix that doesn't depend on the magic number at all?**

The sturdier fix is to stop the wrap and let the column size itself to its content — `white-space: nowrap` with a content-based track such as `max-content` — so a fifth, longer status word can never break it. A hardcoded width is the fragile version of the same fix.

PASS if the self-sizing option appears, whether it is the recommendation or an alternative that is named and weighed.

FAIL if the only fix on offer is a fixed pixel width.

Answer PASS or FAIL.
