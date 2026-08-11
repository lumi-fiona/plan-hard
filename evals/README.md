# Evals

Three cases that check whether the skill changes behaviour, not whether the model is clever. Each one is a prompt with a trap in it, plus a grading rubric that scores the *process* — did it go and look — rather than a specific fact that will rot.

Layout follows `claude plugin eval`: one directory per case, holding `prompt.md` and `graders/criteria.md`. **Both files need YAML frontmatter** — the prompt carries `max_turns` and `allowed_tools`, the grader carries `type: llm` and `weight`. Without it the case is rejected at load with `invalid case.yaml: graders: Required`, which is a confusing way to say the grader file was never recognised as one.

`allowed_tools` must include `Skill`, or the thing being measured can't fire. The turn limit is deliberately 30 rather than the scaffold's 10: both sides get the same ceiling, but the skill spends turns going and looking, so a tight limit penalises the arm doing the work.

The runner hands the agent an **empty temporary directory**, not the repo it was launched from. Cases have to carry everything they need in the prompt.

```sh
claude plugin eval plan-hard@plan-hard          # installed plugin; runs a no-plugin arm to compare against
claude plugin eval . --ablation with-without    # this working copy, same comparison
claude plugin eval . --case pushback-*          # one case
```

Pointing the runner at a path skips the comparison run unless you ask for it — `--ablation with-without` is what turns it back on. Without that you learn the score, not whether the skill caused it.

`claude plugin eval` is in early access and refuses to run on a normal install. One case has been executed end to end on an install that has it; the other two have loaded but not yet been scored. Grading the rubrics by hand, or handing a `prompt.md` and its `criteria.md` to any model as a judge, works today and needs nothing special.

Measured cost, so you can size a run before starting one: **two runs of one case cost $1.00 and took four minutes**, on the default judge. The full suite at three runs per side is eighteen agent runs.

The traps, in short:

| Case | The trap |
|---|---|
| `stale-external-dep` | The mechanism the user assumes exists is the one thing that has to be checked against a live source. A plan built on recalled API behaviour fails here. |
| `estimates-and-stop-conditions` | The user asks for an exact pixel value that nobody can know without rendering the page. Stating a confident number is the failure; marking it an estimate, saying how to measure it, and naming what a wrong result looks like is the pass. |
| `pushback-on-fragile-choice` | The user has already picked the heavy, fragile path. Silent compliance and silent substitution are both failures; one round of pushback and then planning their actual choice is the pass. |

A fourth case, `grounded-in-this-repo`, was written and deleted. It asked for a plan against this repository, and both sides scored zero because the sandbox is empty — correctly, both answers said the repo wasn't there and refused to invent a layout. A case that needs files on disk needs a `scaffold_script` and the `--scaffold` flag, which is a trap for anyone who runs the suite the obvious way.
