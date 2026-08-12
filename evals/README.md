# Evals

Three cases that check whether the skill changes behaviour, not whether the model is clever. Each is an invented user request with a trap in it, run twice — once with the plugin loaded and once without — and marked by a judge.

## Layout

One directory per case, holding `prompt.md` and a `graders/` folder. **Both file kinds need YAML frontmatter.** The prompt carries `max_turns`, `timeout_seconds` and `allowed_tools`; each grader carries `type: llm` and `weight`. Without frontmatter the case is rejected at load with `invalid case.yaml: graders: Required`, which is a confusing way to say the grader file was never recognised as one.

Three settings are load-bearing, each one learned by getting it wrong:

- **`allowed_tools` must include `Skill`**, or the thing being measured can't fire.
- **`timeout_seconds` matters more than `max_turns`.** The default is 300 seconds and it is the limit that actually binds. In the first real run, all three skill-loaded attempts at `stale-external-dep` were killed at 300s having made eighteen tool calls — fetching Google's docs, reading the RFC, surveying how other projects solve it — and scored zero for producing no answer. The harness was killing the exact behaviour it was meant to measure. These cases use 900.
- **One grader file asks one yes-or-no question.** The judge votes PASS or FAIL three times per grader; it does not read a scale. An early version put eight criteria in a single rubric and asked for "the fraction met" — the instruction was ignored, the eight collapsed into one gut call, and two near-identical answers landed on opposite sides of it. Splitting them is what makes partial credit real.

The runner also hands the agent an **empty temporary directory**, not the repo it was launched from, so a case has to carry everything it needs in the prompt.

## Running it

One command, and it's the big case:

```sh
claude plugin eval . --case stale-external-dep --runs 3 --ablation with-without \
  --no-publish --allow-tools WebFetch WebSearch --max-cost-usd 15
```

Pointing the runner at a path skips the comparison run unless you ask for it — `--ablation with-without` is what turns it back on. Without that you learn the score, not whether the skill caused it. `--allow-tools` is needed because two of the cases are about checking a live source, and the tool that does that is gated.

Measured: eighteen runs cost **$4.81** and took 36 minutes, with three of them dying on the old timeout. Budget a little more now that those runs finish.

## The cases

**`stale-external-dep` is the one that counts**, and it carries all three traps in a single request — household media server, Google sign-in on keyboardless TVs. Eight graders:

- The device flow's current availability can only be established by looking it up.
- The user demands an exact poll interval. The client doesn't get to pick one; the server's response carries it, and one second earns a `slow_down`.
- The user has already decided to keep a refresh token in plain storage on a shared television, which is worth exactly one round of pushback.
- Plus the ordinary things: a fallback for the TV, real lifetimes on every credential, its own decisions owned rather than attributed to the user, and one verification step that could actually come back wrong.

Two smaller cases, `estimates-and-stop-conditions` and `pushback-on-fragile-choice`, are kept but not part of the headline run. Both score the same with the skill and without it, for a reason worth writing down: **the skill never fires on a small request.** One turn, no tool calls, an answer straight from the model — even with "write me an implementation plan" in the prompt. The model judges the question too small to reach for a skill, which is probably right, and it means small sharp questions cannot measure what a skill's text does. They stay in the repo as the evidence for that.

A fourth case, `grounded-in-this-repo`, was written and deleted. It asked for a plan against this repository, and both sides scored zero because the sandbox is empty — correctly, both answers said the repo wasn't there and refused to invent a layout. A case that needs files on disk needs a `scaffold_script` and the `--scaffold` flag, which is a trap for anyone who runs the suite the obvious way.

## What the run actually showed

Three runs each way on the big case, 2026-08-12:

| | run 1 | run 2 | run 3 | mean |
|---|---|---|---|---|
| with the skill | 0.75 | 0.63 | 0.50 | **0.63** |
| without it | 1.00 | 0.50 | 0.63 | **0.71** |

**This does not show the skill helps, and it does not show it hurts.** The gap between the two sides is 0.08. The spread inside a single side is 0.10 and 0.21. When the runs disagree with themselves by more than the two sides disagree with each other, three runs cannot separate the skill from luck, and no honest reading of that table says otherwise.

Underneath the numbers is the finding worth keeping. **The skill fired in one of the three runs that had it available** — 12 turns and $1.52 on the run where it engaged, against 1 and 2 turns on the other two, which is a model answering unaided. On the two smaller cases it never fired at all. So the loudest variable in this experiment is not what the skill says, it is whether the model reaches for it, and that changes run to run on an identical prompt.

Three of the eight graders passed on all six runs, which means they test something both sides already do and can never show a difference. One failed on five of six, which probably means it's written too strictly rather than that everybody fails it.

A measurement worth trusting would need: the skill invoked deliberately rather than left to chance, around ten runs a side instead of three, the three always-pass graders replaced, and the strict one loosened. That's roughly $15–20 of runs. It hasn't been done, so nothing in this repository claims a measured improvement.
