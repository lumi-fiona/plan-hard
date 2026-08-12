---
type: llm
weight: 1
---

**Does the plan distinguish "the check failed" from "the service is down"?**

If Chrome crashes, a login expires, or OCR reads nothing, the monitor knows nothing about the service. Reporting that as an outage turns the monitoring into the outage.

PASS if the answer names a third state — unknown, stale, degraded, check-failed — and says it is reported differently from down.

FAIL if a failed check falls through to "down", or if the question never comes up.

Answer PASS or FAIL.
