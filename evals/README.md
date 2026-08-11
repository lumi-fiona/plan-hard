# Evals

Three cases that check whether the skill changes behaviour, not whether the model is clever. Each one is a prompt with a trap in it, plus a grading rubric that scores the *process* — did it go and look — rather than a specific fact that will rot.

Layout follows `claude plugin eval`: one directory per case, holding `prompt.md` and `graders/criteria.md`.

```sh
claude plugin eval plan-hard@plan-hard          # installed plugin; runs a no-plugin arm to compare against
claude plugin eval . --ablation with-without    # this working copy, same comparison
claude plugin eval . --case pushback-*          # one case
```

Pointing the runner at a path skips the comparison run unless you ask for it — `--ablation with-without` is what turns it back on. Without that you learn the score, not whether the skill caused it.

**These cases have not been executed.** `claude plugin eval` is in early access and refuses to run on a normal install, so the suite is written to the documented layout and never proven against the runner. Treat it as a draft harness until someone with access runs it once. Grading the rubrics by hand, or handing `prompt.md` and `criteria.md` to any model as a judge, works today and needs nothing special.

The traps, in short:

| Case | The trap |
|---|---|
| `stale-external-dep` | The mechanism the user assumes exists is the one thing that has to be checked against a live source. A plan built on recalled API behaviour fails here. |
| `grounded-in-this-repo` | The task is small enough to answer from a guess about the layout. Every claim about what this repo already contains is checkable, so invented files are visible. |
| `pushback-on-fragile-choice` | The user has already picked the heavy, fragile path. Silent compliance and silent substitution are both failures; one round of pushback and then planning their actual choice is the pass. |
