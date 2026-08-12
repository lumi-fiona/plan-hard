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

```sh
claude plugin eval plan-hard@plan-hard                                   # installed plugin, compared against no plugin
claude plugin eval . --runs 3 --ablation with-without --no-publish \
  --allow-tools WebFetch WebSearch --max-cost-usd 12                     # this working copy
claude plugin eval . --case pushback-* --runs 1 --ablation with-without  # one case, cheap
```

Pointing the runner at a path skips the comparison run unless you ask for it — `--ablation with-without` is what turns it back on. Without that you learn the score, not whether the skill caused it. `--allow-tools` is needed because two of the cases are about checking a live source, and the tool that does that is gated.

Measured: eighteen runs cost **$4.81** and took 36 minutes, with three of them dying on the old timeout. Budget a little more now that those runs finish.

## The cases

| Case | The trap |
|---|---|
| `stale-external-dep` | Sign-in on a TV using Google's short-code device flow. Whether that mechanism is available for this kind of app is the one thing that can't be answered from memory, and the whole TV half rests on it. |
| `estimates-and-stop-conditions` | The user demands one exact pixel width that nobody can know without rendering the page. Stating a confident number is the failure; marking it an estimate, saying how to measure the real one, and naming what a wrong result looks like is the pass. |
| `pushback-on-fragile-choice` | The user has already chosen headless Chrome plus OCR for uptime checks. Silent compliance and silent substitution are both failures; disagreeing once, then building their actual choice properly, is the pass. |

A fourth case, `grounded-in-this-repo`, was written and deleted. It asked for a plan against this repository, and both sides scored zero because the sandbox is empty — correctly, both answers said the repo wasn't there and refused to invent a layout. A case that needs files on disk needs a `scaffold_script` and the `--scaffold` flag, which is a trap for anyone who runs the suite the obvious way.

## Honest status

The harness runs and the cases load. The scores from the first full run are not usable as evidence about the skill: one case was killed by the timeout, one was graded by a judge flipping on equivalent answers, and the third passed on both sides and so distinguished nothing. Everything above is the fix for those three problems, and the fixed suite has not been run yet.
