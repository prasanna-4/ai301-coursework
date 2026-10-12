---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Is this pull request ready to submit?

A PR package is a candidate pull request (title, description, commits, diff, and test evidence) read against three fixed contexts: the plan it claims to implement (scope, test plan, and any approved deviations), the issue that plan belongs to (the stated problem, thread discussion, and maintainer guidance), and the repository's stated standards (PR template asks, contribution policy, and any AI-use disclosure requirement).

The tool answers only this one question: does the PR match the plan, is the evidence decisive, is the diff clean, and does the PR honor the repository's stated standards? If yes to all four, the PR is ready to submit. If no on any, it holds.

## Inputs and modes

### Live mode

When checking your own PR before opening it:

1. **Your plan.md** (with any deviation notes you added)
   - Read from your local working copy: the week-3 plan document you posted, including any notes about approved deviations from the stated scope.
   
2. **Your branch's diff**
   - Read by running `git diff main...HEAD` (three dots) from your working copy, producing the unified diff of every change on your branch relative to the repository's default branch.
   
3. **Your draft PR title and description**
   - Read from your GitHub draft PR or from a local file you are testing.
   
4. **Your test evidence**
   - Read from a file or transcript you create locally, showing the repro scripts or test commands you ran, with their before and after output.
   
5. **Your issue and thread**
   - Read from GitHub: the issue body, any maintainer comments, and the stated repository policy from the repo's PR template and CONTRIBUTING.md.
   
6. **Scope enforcement (live mode only)**
   - Before grading: read `scope.md` in the tool directory. If the `Repo:` line contains a placeholder (still says `<ORG>/<PATH-REVIEW-REPO>` or equivalent), stop and tell the student to fill in the Repo line with their section's Path Review repository. The tool refuses to grade work targeted at the wrong repository.
   - If the Repo line is filled, verify that the PR is against that repository. Do not grade work outside the scoped repo.
   
7. **Voice guide (live mode only)**
   - Read `voice-guide.md` in the tool directory. After grading the four rubric checks, hold the draft PR title and description against the voice rules stated there. Report any rule the draft breaks in the summary text before the JSON output. The voice guide never changes the verdict on its own (the verdict comes from the rubric alone).

### Eval mode

When grading an eval package bundle:

- The bundle is the whole world: every fact comes from the markdown package text and its structured `.json` twin. Nothing is fetched from the internet, no thread is re-read on GitHub, no scope.md is consulted, and no voice-guide.md is read.
- Every check runs with the full verdict rule as written in the rubric. There are no exceptions, no early stops for placeholder text, and no voice-guide softening.

## The scope seam (live mode only)

In live mode, the tool reads `scope.md` before anything else. The scope file names the repository where the student's PR must live; the tool refuses to grade work outside that repository. If the `Repo:` line in `scope.md` still carries an unfilled placeholder, the tool stops without grading and instructs the student to fill in their section's Path Review repository. It never guesses a scope, and it never proceeds when the scope is uncertain.

Eval mode ignores `scope.md` entirely: the bundle specifies the repo, and the tool grades it as-is.

## The voice seam (live mode only)

In live mode, the tool reads `voice-guide.md` and holds the draft PR title and description against the voice rules there. If the draft breaks a rule, the tool reports the violation in its summary text before the JSON output, so the student can revise before opening the PR. The voice guide never changes the verdict on its own: it is a communication check, not a grading check. If the rubric has no voice-related checks, a voice-rule violation does not affect accept or reject.

Eval mode ignores `voice-guide.md` entirely: voice is personal and not scored.

## Component reads

The tool executes by reading its three component files in order:

1. **rubric.md** defines the checks (name, evidence source, pass condition, weight) and the verdict rule (how per-check grades combine into accept or reject).
2. **procedure.md** defines the concrete steps: what to read first, what to gather from each part, in what order checks execute, and how unclear grades are handled.
3. **references/evidence-guide.md** maps each evidence family to its location in the package and states what good looks like (so the tool's executor can find evidence independently).

If rubric.md has no content (is empty or contains only comments), the tool refuses to grade and says so. If procedure.md has no content or if evidence-guide.md has no content, the tool refuses to grade and reports the gap. An empty tool is better than an inventing one; a template's instruction comments are guides, not content. Only prose you write counts.

## Verdict and output

The tool's reply ends with a fenced JSON block, and nothing follows it. The tool uses one of two verdicts:

- **accept**: The PR is ready to submit.
- **reject**: The PR should hold; revisit the rubric's checks that failed before opening.

The tool first provides a readable per-check summary, then the machine-read result as a fenced JSON block:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

The JSON block is valid, last, and nothing comes after it.

## Grading discipline

These are the standing rules the tool grades under:

- **Evidence first.** No check is graded without naming the fact or quote that decided it. "Looks fine" is not evidence.
- **Grade the thing, not the polish.** A terse complete PR can be ready and a beautiful confident one can be hiding drift. The tool reads the artifact (the diff, the evidence, the description claims) against the plan, the issue, and the stated standards. It grades what it sees, not how it was written.
- **The rubric decides, not the run.** If a check passes by its stated condition but feels wrong, it still passes. The fix belongs in the rubric, not in this frame.
- **The procedure decides how, not the run.** The tool follows procedure.md as written. If the procedure is silent on a step, the tool reports the gap instead of silently inventing one.
- **Unclear defaults to fail.** Where a claim is unverifiable from the package (the test evidence is ambiguous, the file list is unclear, the disclosure is missing), the tool treats `unclear` as a fail. An unverifiable PR is not one that is ready to submit.
