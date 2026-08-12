---
type: llm
weight: 1
---

**Did the answer check the mechanism against a live source instead of describing it from memory?**

The user assumed Google's device authorisation flow — the one where a TV shows a short code — is available for what they want. Whether it currently exists, is restricted to certain client types, requires app verification, or has been changed is exactly the thing a plan must not answer from training data.

PASS if the answer cites at least one specific source it consulted for this claim — a documentation URL, the RFC, a changelog — in a way that shows it looked rather than recalled.

FAIL if the flow is described confidently with no source, even if the description happens to be accurate. Confidence is not the same as verification, and that is the whole point of this case.

Answer PASS or FAIL.
