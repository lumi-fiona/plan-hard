---
name: plan-hard
description: Produce verified, codebase-grounded, red-teamed implementation plans instead of plausible-but-unchecked ones. Use whenever the user asks for an implementation plan, design, architecture proposal, RFC, migration strategy, or "how should we build X" — and any time plan mode is active or a plan is about to be presented, even if the user doesn't name this skill. Especially critical when the design depends on external services, third-party tools (yt-dlp, ffmpeg, scrapers, extractors), platform APIs, or auth flows, where stale training data produces confidently dead mechanisms.
---

# Plan Hard

A plan is a set of claims. This skill's one job: make every load-bearing claim **verified**, **grounded in the real code**, or **visibly marked as an assumption** — never confidently wrong.

It exists because of a real bake-off: three AI plans for the same feature were adversarially fact-checked against primary sources and the actual repo. The ranking had almost nothing to do with model intelligence:

- The most sophisticated plan built its flagship on an API that had been dead for over a year (never checked) and cited a flag as "confirmed working" that actually crashes.
- Another followed the user into a heavy custom component without naming the tradeoff, and its verification section promised an isolation guarantee its own design contradicted.
- The winner verified external reality against current sources, reused what existed, kept fallbacks, counted its new modules on one hand, and was honest about what it didn't know.

The difference was discipline, not brains. Apply the six disciplines below **before** presenting any plan. Everything here is read/research work — fully compatible with plan mode. Scale depth to stakes: a small refactor needs minutes of this; an architecture that other users, devices, or services will depend on needs all of it. When the plan is large, fan the research out to parallel subagents (one for codebase verification, one for external facts, one for prior art) rather than trimming the list.

## 1 · Separate what was asked from what you're deciding

Sort every requirement into three buckets before designing anything:

- **Stated** — the user's words. Quote them; don't paraphrase extra scope in.
- **Derived** — necessarily implied (e.g., "sync across devices" ⇒ credentials must live server-side).
- **Decided by you** — everything else: storage shapes, fallback policy, capture mechanisms, strictness levels, naming.

The third bucket is where plans go wrong. Never phrase your decision as the user's ("the user chose strict mode", "as required"). Every entry in that bucket goes in the plan's **Decisions** table: chosen default, the alternative, one line of why. For the load-bearing decision, show your work: name the alternatives you *rejected* and the one-line reason each loses (a cost, a failure case, a bound it can't meet). A choice with no visible losers is a preference, not a decision.

**Defaults + pushback protocol:**

- Pick the defensible default yourself and document it. Defensible means: what the field consensus does, what degrades gracefully, what's reversible later.
- Ask the user only when a fork is *product-level* — it changes what they get, not how it's built. When you ask, present tradeoff + recommendation, never a bare menu of options.
- If the user has already directed an approach that is the heavier or more fragile path, push back **exactly once**: what it costs, what the lighter path is, your recommendation. Then respect their call and plan their choice properly — including the degradation path the fragile choice needs. A collaborator who never pushes back is how users walk into choices they later call "my mistake".

## 2 · Ground every claim in the real codebase

Recall is not reading. Before writing implementation steps:

- **Read every file the plan touches.** Every one. A plan written from memory of the codebase is a plan about a codebase that doesn't exist.
- Every "X already exists" gets verified; every proposed new symbol gets confirmed absent. Renaming something that isn't there, or re-inventing a helper that lives three files over, destroys trust in the whole document.
- **Find the house pattern first.** The repo's existing idiom for per-user secrets, caching, routes, validation, or persistence is your template. Following it is simultaneously lazier and more correct than inventing a new one.
- Cite `file:line` for load-bearing anchors — and re-check the cites at the end; code moves.
- **A document about the code is not the code.** Every `file:line` in the plan must trace to a Read/Grep of that file *this session*. Anchors inherited from a review doc, a memory note, or project docs are hypotheses — re-verify them against the tree or mark them stale. Documents freeze; code moves; a stale citation from your own past report is still a stale citation.
- **Look at the thing, not just its source.** If the plan touches UI or UX, recon includes the *rendered* app — run it and screenshot it (desktop + narrow viewport) — or the Context section carries an explicit `Not looked at: <reason>` line. Clutter, hierarchy, and what the eye actually lands on are invisible in the code; suggestions grounded in the rendered UI outrank ideated ones. Declaring the skip is mandatory — silent code-only UX review is how the biggest gap in a plan stays unfound.
- **Enumerate every call-site** (kernel rule 2, applied to plans): a gate on the route is worthless if a background job calls the same mutation directly — grep every path that reaches it before placing a guard.
- **A plan whose anchors all live in one large file invalidates them itself.** Its own first task shifts every line number below the edit, so by task three the citations point at nothing — measured at +127 lines across one stylesheet in a single stage. Either re-grep per task, or say once at the top of the plan that the numbers are stale and the selector is the anchor. Do not silently hand a later task a number that was true when you wrote it.
- Write down what the plan does **not** touch. Scope honesty is what lets a reviewer trust the rest.

Watch for the classic threading traps whenever identity, tenancy, or entitlement flows through existing code:

- **Shared caches** — if two users can legitimately get different results, the cache key must include who's asking. A "per-user" feature layered over a globally-keyed cache silently serves one user's entitlements to another.
- **Shared files and queues** — who owns each entry? What happens with concurrent writers? Is the write atomic, or can a crash mid-write corrupt it?
- If your verification section will claim "user A never sees user B's X", locate the exact design element that enforces it *now*. A promised guarantee with no enforcing mechanism is the single most common self-contradiction in plans.

## 3 · Verify external reality — the discipline that decides rankings

Any mechanism you don't control is a claim, not a fact. List every load-bearing external dependency: platform APIs, auth flows, scraping targets, third-party tool behaviors, service capabilities, OS features.

Triage by volatility:

- **Adversarial or fast-moving** (auth to major platforms, scraping, undocumented/internal APIs, extractor tools, anything with a cat-and-mouse dynamic): your training data is stale *by default*. Verify against primary sources dated within the last few months — official docs/wiki, the project's issue tracker (search "no longer works", "deprecated", "broken", plus the current year), releases/changelogs, context7 for libraries. One targeted fetch of current docs beats ten reasoned paragraphs.
- **Stable** (HTTP semantics, SQL, POSIX, mature stdlib): memory is fine. Don't perform verification theater on things that can't have changed.

Rules that would each have saved a real plan:

- **The flagship mechanism must be verified alive, not assumed alive.** If the whole design rests on it, a five-minute check is not optional — it's the cheapest insurance in the entire process.
- **Probe the mechanism, not just the claim.** When the design depends on data X being available from source Y — timestamps in a page, a field in an API response, a stream that actually plays — make one real request and confirm X is actually there. Docs are testimony; a probe is evidence. The most valuable finding a plan can contain is "the proposed mechanism cannot work because its input doesn't exist," and you only get that by looking, never by citing.
- **Spend verification depth where the plan dies first.** Rank assumptions by blast radius: the one that kills the whole design if wrong gets the live probe; peripheral facts get one source; stable facts get memory. Spreading effort evenly across a checklist is compliance, not verification.
- Never write "confirmed working" without a source you actually fetched this session. A confident citation of something that crashes is worse than no citation, because it disarms the reviewer.
- **Survey prior art** — find 2–3 comparable tools that solved this problem and note what they ALL do. Unanimous convergence is strong evidence: if every comparable tool keeps a shared fallback and your design doesn't, the plan needs a sentence explaining why you know better than the field. If you can't write that sentence, follow the field. Survey *user moments*, not just mechanisms: when the plan shapes an experience (staring at content, first run, an error path), ask what every comparable product does at that same moment — UX convergence (every video player fades its chrome on idle) is as load-bearing as API convergence, and it's the half of prior art plans routinely skip.
- Record verified facts **with source links** in the plan's Context section. Everything unverified gets marked as an assumption, with a default and an upgrade path — an honest "decide during implementation, flagged" beats a fabricated certainty.

## 4 · Prescriptions are hypotheses until they've been run

The disciplines above verify what you *cite*. This one verifies what you *propose*, and it is the one that gets skipped: a plan is mostly proposals, and a proposal written in the grammar of a specification reads as a fact to whoever implements it.

**Any exact value or exact declaration in a step must come from a measurement taken this session, not from a derivation.** A CSS declaration, a pixel width, a constant, a threshold, a selector, a query shape — if you reasoned your way to it instead of running it, it is a hypothesis, and the step must say so and carry the measurement that settles it.

This is the observed failure rate, not a worry — kernel rule 1 carries the incident (2026-07-29: a fifteen-task plan whose every cited `file:line` was verified correct still got eleven of fifteen *prescriptions* wrong; the implementers won for one reason — they measured before typing). What follows from it:

- **Write the goal, the constraint, and the check — let the implementer derive the declaration.** Where the result depends on a live context you cannot hold in your head (a CSS cascade, a query planner, a layout engine, a scheduler), an exact *wrong* block is worse than a described goal, because it invites transcription instead of thought.
- **Arbitration with `writing-plans`:** that skill demands exact code blocks and forbids placeholders, and it is right for deterministic code. In cascade-dependent domains this discipline wins — but the step must then carry a *runnable measurement* in place of the block, never a vague instruction. "Add appropriate spacing" is still a plan failure; "measure the widest rendered stamp and size the column to it; stop if it exceeds 88px" is not.
- **Every step that ships a claim carries the check that could falsify it**, and the check names the number that would fail. The two highest-value steps in that plan were both this shape: *"measure the widest stamp; if it exceeds 88px, stop and escalate"*, and *"press Sync, re-check, and if the copy still reads false, halt rather than ship the sentence."* The second fired, disproved its own brief, and stopped a false statement reaching the user.
- **A stop condition is worth more than a correct value.** You will be wrong about values. A step that knows what wrongness looks like survives being wrong.
- **Mark a derived value as derived.** "Set it to 72px" and "set it to the measured widest stamp — 72px is my estimate, confirm it" are the same instruction with opposite failure modes.

## 5 · Design lazy, degrade gracefully

Climb the laziness ladder and stop at the first rung that holds: does this need to exist at all → does the codebase already have it → does the stdlib or the platform do it → does an already-installed dependency do it → can it be one line → only then, the minimum new code. Planning adds one rung before "write new code": **an existing third-party tool the user can simply use** (a browser extension, a CLI) instead of one you build.

- A new package, workspace, service, or app is a **last resort** and its justification gets written into the plan. "It would be smoother" is not a justification; "it is the only mechanism that can technically do X (verified — see source)" is.
- **Keep fallbacks.** Removing an existing working path needs explicit user sign-off. Strict no-fallback postures convert every routine decay event — an expired credential, a rotated cookie, a rate limit — into a hard outage.
- **Design for decay.** Anything that expires, rotates, rate-limits, or gets revoked gets three things: a realistic lifetime *number* (never "long-lived" — write "~3–14 days"), a visible degraded state in the UX, and a reconnect path. "Automatic once set up" is only honest if you designed for the day it breaks.
- Always include the **Cheaper alternative** section: the smaller rung, honestly scoped — what it delivers, what it gives up, roughly how much less work it is. Give the user the option to be lazy; it's their time and money.

## 6 · Red-team your own plan before presenting it

Draft first, then attack the draft. Self-review by the model that wrote the draft shares its blind spots — the sweeps below reliably catch the *mechanical* classes, but a same-context reviewer passes its own assumptions. When the plan is large or high-stakes, run the sweeps as a SEPARATE fresh-context subagent: give it the plan file, repo access, and this sweep list, prompted only to attack (never to fix or praise); its findings are the sweeps' output. Solo self-attack is the fallback for small plans, not the default for big ones.

Seven sweeps, in order:

1. **Contradiction sweep** — for every guarantee anywhere in the plan ("isolated", "can't happen", "atomic", "never leaks"), point at the exact design element that enforces it. A verification scenario the design can't actually pass is a lie you told yourself first.
2. **Safety-assertion sweep** — every "X still works because Y" or "untouched, so unaffected" sentence either becomes a failable scenario in Verification or gets deleted. An assertion of safety is a claim without a test; the wrapper div that "can't affect" the animation reading its ancestor's rect is exactly where plans break. The mirror image gets the same sweep: every "I can't verify X" / "needs the user's eyes" in the Verification section is itself an untested claim — spend one cheap probe on whether the check is actually infeasible before delegating it (kernel rule 1's 2026-07-23 incident: the "unverifiable" render-count check fell to a ~10-minute window-exposed-store harness on the first try). Genuine limits (GPU compositing, real audio flow in headless) survive the probe; excuses don't.
3. **Two-week test** — for each mechanism: what breaks it in *normal use* within two weeks? Expiry, rotation, rate limits, concurrent writers, disk growth, restarts, a second user showing up.
4. **Shared-state audit** — every cache, file, and queue the change touches: is the key still correct, is the write atomic, where are the races?
5. **Cite check** — re-verify every `file:line` and "already exists" against the tree; re-fetch nothing, but re-confirm the one external fact everything rests on is the one you actually verified. Anchors that entered the plan from a doc or memory rather than a this-session read of the file are the first place to look.
6. **Gap-class scan** — check the recurring classes your change touches: a UI hide is not access control; an in-memory cap is not a disk bound; a gate fails closed while a filter fails open; a new flag/param must reach every reader; check-then-mutate needs a lock; a cross-cutting invariant needs a written scope. These are the bugs that pass review because each looks fine locally.
7. **Honesty pass** — at least one real risk listed against your own design (zero risks means you didn't look), lifetime numbers present for every perishable, assumptions marked, no decisions wearing the user's clothes. And remember: "I reviewed it and it looks right" is not a check — a plan that would skip verification also passes its own introspection. Every verification scenario must be something that can *fail*.

Fix what the sweeps find, or downgrade the claim to an assumption. Presenting a plan whose verification section you couldn't personally defend is the exact failure mode this skill exists to kill.

## Model and effort selection

Nothing here needs the most expensive model everywhere, and spending evenly is the same mistake as verifying evenly.

Pick by **whether the task requires diagnosis**, not by diff size — that distinction is the whole point of §4. A ten-line CSS change in a live cascade is not mechanical; a two-hundred-line transcription of already-verified code is.

**Route from measurements you have, not from a tier list.** A hardcoded model ranking goes stale the day a model ships, so this skill deliberately doesn't carry one. If your setup keeps a routing file of measured per-config notes (fits, limits, evidence), read it at plan time and stamp each plan step with `stamp: <model@effort> · <fit, 3–6 words> · <evidence>`, so the executor inherits a recommendation with something behind it instead of vibes. If you have no such file, the shape is still the same: diagnosis and review get the strongest tier available, mechanical and looking-work get the cheap ones, and a model you have never measured on this kind of task is named as unmeasured rather than guessed at.

**Never review with the same context that wrote the draft.** Model diversity helps; *context* diversity is the requirement — a fresh instance of the same model catches what the authoring context cannot see.

Harness facts that constrain the above, verified 2026-07-29 in Claude Code (re-verify any dated fact before a plan leans on it):

- **Browser work must be sequential.** The Playwright MCP drives one browser; parallel agents racing `browser_navigate` interleave and silently corrupt each other's measurements.
- **`browser_evaluate` runs in an isolated world.** Patching `window.fetch` inside it intercepts nothing the app does — and reports success. Response rewriting must go through `page.route`. That is a false-pass channel in the tooling itself, so a verification built on it proves nothing.
- **Turn count beats token price.** A cheap model that takes 3× the turns on multi-step work costs more overall. Mid-tier is the floor for anything working from prose rather than from complete code.

## Output shape

Use this structure — it's lifted from the bake-off winner:

```
# <Plan title>
## Context — what's true and how I know it
stated requirements · verified external facts (with links) · existing-code facts (file:line) · assumptions (marked as such)
## Design
the core mechanism (verified alive) · what's reused · what's new (count it)
## Decisions
table: decision · chosen default · alternative · why   (+ the one pushback, if any applies)
## Steps
numbered · exact files · what changes in each
## Risks
honest ones, including against your own design · lifetime numbers for perishables
## Cheaper alternative
the smaller rung, honestly scoped
## Verification
concrete scenarios: happy path · empty/zero state · expiry/decay · isolation (multi-user) · concurrency · degradation — each one failable
## Explicitly untouched
## Found along the way   (optional)
real issues discovered during recon that are out of scope — listed here, never silently dropped
```

A final warning about the template above: **the sections are not the point — the work behind them is.** A plan can fill every heading and still be unverified; headers are compliance, probes are evidence. If a section exists but the work didn't happen, do the work or delete the section. And the strongest sign your recon was real: you found something nobody asked about — a pre-existing bug, a stale doc, a dead code path — and captured it in "Found along the way."

## Red flags — catch yourself doing one of these, stop and fix it

- "confirmed working" or "still supported" with no source fetched this session
- an assumption wearing the user's clothes ("the user chose…", "as required…")
- a new package/workspace/service where reuse or an existing tool works
- durability adjectives without numbers ("long-lived", "kept alive", "forever")
- flagship mechanism status recalled from memory in a domain with a cat-and-mouse dynamic
- a verification scenario your own design can't pass
- zero risks listed against your own design
- diverging from unanimous prior art without a written reason
- a `file:line` anchor copied from a doc, review, or memory instead of read from the file this session
- a UI/UX plan whose recon never rendered the UI, with no declared `Not looked at:` reason
- an "X still works / unaffected" assertion with no matching verification scenario
- a verification step delegated to the user ("needs your eyes") without a probe showing the check is actually infeasible for you
- an exact value, constant, or declaration in a step that you derived rather than measured — and did not mark as an estimate
- a step that prescribes editing a shared symbol the same plan flagged as shared, without naming what the other callers do with it
- a step with no failure condition: nothing in it that could come back wrong, so nothing that tells the implementer to stop
- a check whose passing state is indistinguishable from its not-having-run state (a hidden element measuring zero, an empty result set, a selector that matches nothing)
- a plan citing many line numbers in one large file, with no note that its own first task invalidates them
- reaching for the most capable model on every subagent because the task felt important, rather than because it needed diagnosis
