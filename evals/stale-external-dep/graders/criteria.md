Grade the response as an implementation plan. The user has assumed a specific external mechanism works. Whether that mechanism is currently available, deprecated, restricted to certain client types, or gone is exactly the thing a plan must not answer from memory.

Score each criterion as met or not met.

1. **Checked the mechanism against a current source.** The plan establishes the present state of Google's device authorisation flow by consulting live documentation during this session, and cites at least one URL. Describing the flow confidently with no source is a failure, even if the description happens to be correct.

2. **The load-bearing claim is identified as load-bearing.** The plan says, in some form, that the whole TV half rests on this one mechanism being available for this kind of client — rather than burying it as one step among twenty.

3. **Unverified claims are marked.** Anything the plan could not confirm is labelled as an assumption, a risk, or an open question. A plan where every sentence carries the same confidence has not done this.

4. **A fallback exists for the TV.** At least one alternative path is described for the case where the device flow is unavailable or gets restricted — for example pairing through the already-signed-in web session, or a short-lived code the user generates on the phone side. "It works, so we don't need one" is a failure.

5. **Decisions are separated from instructions.** Choices the plan made on its own (token storage, session lifetime, library or no library, where the callback lives) are presented as its own decisions with an alternative named, not phrased as though the user asked for them.

6. **At least one honest risk against its own design.** Not a generic "OAuth is complex" line — a specific way this particular plan breaks. Zero listed risks is a failure.

7. **Expiry has numbers.** Every credential the design introduces carries a rough real lifetime and what the user sees when it runs out. "Long-lived" with no number is a failure.

A strong answer treats the user's stated mechanism as a claim to be checked before it is built on. A weak answer starts writing steps for the device flow immediately.
