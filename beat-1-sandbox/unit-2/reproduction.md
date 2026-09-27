# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

prasanna-4

---

## Posted upstream

**Claim comment**

[TODO: permalink to the claim comment, then paste the posted text underneath]

**Reproduction comment**

[TODO: permalink to the reproduction comment, then paste the posted text underneath]

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Run 1 — partial, calibration only
(`--only calib-01,calib-02,calib-03,calib-04 --include-calibration`): all four agreed with
the gold labels (calib-01 `accept`, calib-02/03/04 `reject`). The harness printed
`agreement: 0/0 scored items`, because the four calibration packages are never scored; the
4/4 is my own tally of the `agree` column. I ran this first deliberately — it covers four of
the five categories for about $0.80, including the calib-03 formatting trap and the calib-04
no-environment borderline, so it was a cheap way to find out whether the rubric was worth a
$4 full run before spending one.

Run 2 — full, 20 scored packages, with `--save-run eval-run.txt`:
`agreement: 20/20 scored items  (bar: 18/20: PASS)`, and
`categories: clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`.

That is the whole history: one calibration run, then one full run that passed, so there was
no revise loop and no `--only` re-grade of a disagreement. The last score, 20/20, is the
agreement line in the committed `eval-run.txt`.

**Package analysis**

`pkg-20` (ghostty-org/ghostty#13604), the eval set's single `disclosure` package. My rubric
decided `reject`; the gold label is `reject`; they agree.

What makes it worth naming is how narrowly it rejected. Nine of my ten checks passed it, and
the run's per-check output shows the repro itself is genuinely good: `artifact-shows-the-issue`
passed on "Single-theme run returns `^[[?997;2n` (wrong/light) and conditional-pair returns
`^[[?997;1n` (correct/dark), matching the issue's exact reported symptom", and
`control-or-contrast` passed because the conditional-theme-pair run isolates the trigger. The
single failure was `policy-respected`: "AI_POLICY.md requires disclosing AI use (tool + extent)
in all contributions including issue comments; neither comment discloses it."

My rubric read it that way because `policy-respected` is `required` and my verdict rule is
"Any single `required` check graded `fail` makes the verdict `reject`" — no weighing of one
check against nine, no volume to buy the miss back with. That is deliberate: the assignment
notes this is the one-package category the floor exists for, and a rubric with no conventions
check cannot reach it at all. It is also the check that decides a package a proof-only rubric
would confidently accept, since every proof signal here is clean.

The sharper half of the same design shows on `pkg-09` (sharkdp/fd#2033), a gold `accept` that
a careless version of this check would have rejected. fd's policy does discuss AI and does ask
for a tool-and-extent statement — but only in the pull request, and it says so explicitly for
issue comments. My rubric passed it: "Repo facts: no disclosure ask for issue comments; both
comments are specific and first-person, satisfying the own-words expectation." One check has
to get both of those right, in opposite directions.

**Check rationale**

The `policy-respected` row of `tools/repro-check/rubric.md`, as it now reads:

> | `policy-respected` | The repo-facts block's contribution-policy line (live: CONTRIBUTING.md, AI_POLICY.md, and the bug-report template in the repo), read against both comments. Assume the package was AI-assisted: these comments are produced in a course that uses AI tooling, so treat any disclosure requirement as one that applies. | Pass by default: silence passes, and a policy that only welcomes AI use, asks the contributor to understand and take responsibility, or restricts AI use in pull requests and code, needs nothing in an issue comment. Fail only when the repo's stated policy requires disclosing AI use in contributions that include issue comments and neither comment discloses it. Where the policy instead requires that comments be in the contributor's own words, pass if the comments are specific and first-person rather than generated-sounding boilerplate, and fail if they are not. Read the policy's scope literally: a disclosure requirement written for pull requests only does not bind an issue comment. | required |

Three things in it are deliberate, and each one is there because the obvious shorter version
breaks a specific package.

First, "Assume the package was AI-assisted." Without it the check has an escape hatch: a
grader can reason that maybe no AI was used, so there is nothing to disclose, and pass
`pkg-20` on a technicality. The assumption closes that off and matches the course's own
framing of this work.

Second, "Read the policy's scope literally." I rejected the shorter rule — policy mentions AI,
so the comment must disclose — because it fails `pkg-09`, where the disclosure ask is written
for pull requests and the policy explicitly exempts issue comments. That would have cost me a
clear-accept and taught the rubric the wrong lesson.

Third, the own-words branch. `pkg-03` (ripgrep) has a policy that constrains comments without
asking for disclosure at all: comments must be human-written, and AI-generated ones may be
hidden. A disclosure-only check has no way to pass that correctly for the right reason. The
branch gives the check a second, different way to read a policy that binds comments.

The pass-by-default framing is the fourth decision and the quietest one. Most repos in the set
say nothing about AI, and a check that starts from suspicion would have leaked failures across
`pkg-01`, `pkg-11`, `pkg-12` and the rest. Starting from pass and naming the two narrow
conditions that fail keeps the check to the one package it is for.

**Trade-offs**

The "read the scope literally" clause is where this check pays. It will miss a repo whose AI
policy is real but loosely drafted — one that says "disclose AI use" in a CONTRIBUTING.md
without ever saying where, or that means comments but only writes about pull requests. My
check reads that as not binding an issue comment and passes it, and a maintainer who meant
otherwise would be right to be annoyed. I accept that miss because the opposite error is worse
and better evidenced: `pkg-09` is a gold accept that the loose reading rejects, so tightening
the clause would cost me a package I currently get right in exchange for a package that is
not in the set.

Nothing changed elsewhere, and here is how I know. There was no revision to this check after a
disagreement — the calibration run came first and agreed 4/4, the full run agreed 20/20 on the
first attempt, and no package was ever re-graded with `--only`. So no canary was needed: the
`disclosure` category never flipped, because it was never loosened after being measured. The
`categories` line in `eval-run.txt` records `disclosure 1/1` on that single full run.

One check did fail on a gold accept without changing anything, which is the trade-off working
as designed: `control-or-contrast` failed `pkg-09` ("Padding re-run did not isolate the
trigger; report states both commands still flushed at the same boundary"). It is weighted
`preferred`, so the verdict stayed `accept`. A contrasting run is the single best signal in
the set, and I wanted it reported — but a faithful single failing run is postable without one,
and `pkg-09` is exactly the honest cannot-reproduce that would have been punished if I had
made it `required`.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
