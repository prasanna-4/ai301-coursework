# Procedure: how this skill grades a plan package

These are the operating steps. Follow them in order. Where a step says
"record", write the fact down before moving on: every check below is
graded from the recorded notes, not from a fresh read, so two
executors who follow these steps reach the same grades.

## Read order

Read the whole package once, in this order, before grading anything.
The order matters because the rubric's first standing rule makes the
repro evidence outrank the issue and the thread: if you read the
plan's confident diagnosis first, you will read the evidence looking
for agreement with it. Reading the evidence first means you already
know what the artifacts show when the diagnosis arrives.

1. Read the `## Repo facts` block. Record two things: the
   contribution-policy line verbatim, and whether that policy asks for
   anything of an issue comment (disclosure, own words, or nothing).
2. Read the `## Issue` section. Record, in one sentence each: the one
   behavior being reported, and the trigger that produces it.
3. Read the `## Repro evidence` block. Record: the artifacts it shows,
   every control run and what each control proves or rules out, and
   the `Actual:` line's statement of which variable flips the outcome.
   This record is the ground truth for the rest of the grading.
4. Read the `## Thread highlights`. Record every comment from an
   OWNER, MEMBER, COLLABORATOR, or CONTRIBUTOR that names a culprit,
   proposes or rejects an approach, posts a patch or PR number, or
   asks contributors for something. If no comment carries a repo role,
   record "no maintainer direction in thread".
5. Read the `## Candidate plan`. Record: its stated cause, every
   change it proposes (as a list, one line each), the code locations
   it names, its test plan, and anything it marks as unknown.
6. Read the `## Candidate plan comment` last, and read it as a
   maintainer scrolling the thread would: alone, without the plan
   beside it. Record what it commits to and what it acknowledges from
   the thread.

In live mode, substitute per `references/evidence-guide.md`: the
issue, thread, and repo policy come from GitHub, the repro evidence
from the student's posted repro comment on that issue, and the plan
and comment from `plan.md` and `comment.md`. Read `scope.md` before
step 1 and stop if the issue is outside the scoped repo or the repo
line still holds a placeholder.

## Evidence gathering

One gathering move per check. Each pulls from the notes recorded
above; go back to the package text only to quote a line exactly.

1. `cause-grounded`: take the plan's stated cause (step 5) and set it
   beside the control runs and `Actual:` line (step 3). For each
   control, write one line: does this control agree with the stated
   cause, or rule it out? A control rules a cause out when it
   exercises the blamed component and the failure does not appear, or
   when it shows the failure already present before the blamed step
   runs, or when it isolates a different variable as the one that
   flips the outcome.
2. `scope-bounded`: take the change list (step 5) and the one reported
   behavior (step 2). Mark each change `needed`, `test-or-doc`, or
   `extra`. A change is `extra` when the reported behavior would be
   correct without it.
3. `approach-chosen`: from step 5's notes, quote the most specific
   code location the plan names, and quote the sentence stating what
   will be done there. If either is missing, record it as absent.
4. `test-plan-observable`: from the test plan (step 5), quote every
   stated expected outcome. For each, record whether it is something
   you could watch come out differently before and after the fix, or
   a generality.
5. `comment-engages-thread`: set the comment's content (step 6)
   beside the maintainer-direction record (step 4). For each recorded
   direction, mark whether the comment follows it, builds on it,
   gives a reason for departing from it, or does not mention it.
6. `policy-disclosure`: set the policy line (step 1) beside the
   comment (step 6). Record which of the three the policy is —
   disclosure required of issue comments, own words required, or
   nothing asked of comments — and then whether the comment meets it.
7. `unknowns-marked`: list from step 5 what the plan marks as a guess,
   unknown, or to-confirm.

`references/evidence-guide.md` says where each of these lives in a
package and what good looks like there. When a gathering move comes
back empty, that emptiness is the evidence: record "absent" and carry
it into the grade. Do not substitute a different part of the package
for the one the check names.

## Check execution

1. Grade the checks in rubric-table order: `cause-grounded`,
   `scope-bounded`, `approach-chosen`, `test-plan-observable`,
   `comment-engages-thread`, `policy-disclosure`, `unknowns-marked`.
2. Grade every check, even after one has failed. The verdict needs
   only one required fail, but the author needs the whole report, and
   stopping early hides the other problems from them.
3. Grade each check only from the evidence gathered for that check
   above. Do not re-read the whole package per check, and do not let
   another check's finding decide this one: a plan with a wrong cause
   can still have a bounded scope, and it is graded as having one.
4. Apply the rubric's pass condition as written. If the stated
   condition passes something that feels wrong, grade it `pass` and
   note the tension in the summary; the fix belongs in the rubric.
5. Grade `fail`, not `unclear`, when the evidence a check needs is
   absent from the drafts and its absence is what the check is about —
   a plan that names no cause fails `cause-grounded`, a plan with no
   test plan fails `test-plan-observable`. Reserve `unclear` for the
   case where the package genuinely does not let you tell, for example
   a repo-facts block whose policy line is missing entirely.
6. Write the deciding fact for each check as one line, quoting the
   package where a quote is shorter than a paraphrase. "Looks fine" is
   not a deciding fact; name the control run, the change, the location,
   the expected outcome, the thread comment, or the policy clause.

## Verdict assembly

1. Collect the grades for the six `required` checks. Set the
   `preferred` check aside: it is reported, never counted.
2. Convert each `unclear` on a required check to a fail for counting
   purposes, per the rubric's verdict rule.
3. If every required check is `pass`, the verdict is `accept`.
   Otherwise the verdict is `reject`. There is no third verdict and no
   weighing of how many failed: one is enough.
4. Name the deciding check in the summary. On a `reject`, that is the
   first required check graded fail in table order; quote its deciding
   line. On an `accept`, say that all six required checks passed and
   name the one that was closest to failing, so the author knows where
   the plan is thinnest.
5. Emit the JSON block in SKILL.md's format, with one entry per check
   in the same table order, each carrying its grade and its one-line
   deciding fact, and the verdict from step 3. The JSON block is last
   in the output; nothing follows it.
6. In live mode only, before the JSON block, report any
   `voice-guide.md` rule the draft comment breaks, quoting the rule.
   Voice notes never change the verdict computed in step 3.
