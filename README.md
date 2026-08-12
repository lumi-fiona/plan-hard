# plan-hard

A planning skill for [Claude Code](https://claude.com/claude-code). It makes Claude verify a plan before showing it to you, instead of handing you something that merely sounds right.

A plan is a set of claims. This skill's one job is to make every load-bearing claim **verified**, **grounded in the real code**, or **visibly marked as an assumption** — never confidently wrong.

## Install

Two commands inside Claude Code:

```
/plugin marketplace add lumi-fiona/plan-hard
/plugin install plan-hard@plan-hard
```

Then start a new session. Claude picks the skill up on its own whenever you ask for an implementation plan, a design, an RFC, or a migration strategy — you can also just say `use plan-hard`.

To pick up later versions:

```
/plugin marketplace update plan-hard
/plugin update plan-hard@plan-hard
```

The `@plan-hard` suffix is required — the bare name reports "plugin not found". Restart the session afterwards to load the new version.

### Without the plugin system

Copy the skill folder into your skills directory:

```sh
git clone https://github.com/lumi-fiona/plan-hard
cp -r plan-hard/skills/plan-hard ~/.claude/skills/plan-hard
```

Per-project instead of global: copy it to `.claude/skills/plan-hard` inside the repo.

## What it actually does

Six disciplines, applied before a plan is presented:

1. **Separate what was asked from what you decided.** Everything Claude chose on its own goes in a Decisions table with the alternative it beat. No decision gets to wear your clothes.
2. **Ground every claim in the real codebase.** Every file the plan touches gets read this session. "X already exists" gets checked; a `file:line` copied out of a doc or a memory is a hypothesis, not a citation.
3. **Verify external reality, don't recall it.** Anything involving a third-party tool, a platform API, or an auth flow is stale by default — training data is old, and a flagship built on a dead API is the exact failure this came from.
4. **Write steps that can fail.** A step carries the check that would falsify it, and the check names the number that would fail. A stop condition is worth more than a value you guessed.
5. **Design lazy, degrade gracefully.** Reuse before building; keep fallbacks; anything that expires gets a real lifetime number and a reconnect path; always offer the cheaper version of the plan.
6. **Red-team the draft before presenting it.** Seven sweeps, including a contradiction sweep, a two-week test, and an honesty pass. A plan with zero listed risks means nobody looked.

## Checking that it does anything

`evals/` holds an eval suite that runs the same request twice, once with this plugin loaded and once without, and marks both against eight yes-or-no graders. It has been run. **It did not show that the skill improves anything** — with the skill the three runs scored 0.75, 0.63 and 0.50; without it, 1.00, 0.50 and 0.63. The runs disagree with themselves by more than the two sides disagree with each other, so three runs each way cannot separate this skill from luck.

The more useful thing it found: on identical prompts, the skill fired in one run out of three, and never on smaller requests. Whether the model reaches for the skill matters more than what the skill says.

[evals/README.md](evals/README.md) has the numbers, the graders, and what a measurement worth trusting would actually take. Until someone does that, treat this repository as a discipline that is argued for, not one that is proven.

## Where it came from

Three AI plans for the same feature were fact-checked against primary sources and the actual repository. The most sophisticated plan built its centerpiece on an API that had been dead for over a year, and cited a flag as "confirmed working" that actually crashes. The winner verified external reality, reused what existed, kept its fallbacks, and was honest about what it didn't know.

The difference was discipline, not intelligence. This skill is that discipline, written down.

## License

MIT
