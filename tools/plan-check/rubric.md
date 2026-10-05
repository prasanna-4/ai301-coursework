# Rubric: is this plan ready to post and build from?

Every check below judges the plan itself, not the write-up's shape.
Length, headings, section names, bullet style, and tone never decide a
check. A six-line plan that names a cause, a file, and an observable
test can pass every check; a plan with a Diagnosis heading, a Scope
heading, and a Risks heading can fail most of them.

Three standing rules for every check:

- **Grade what the drafts contain and quote.** Work the author did but
  did not write down is not evidence. A plan that would be fine if the
  author explained it in person is not fine on the thread.
- **The repro evidence outranks the issue and the thread.** When the
  issue body or a maintainer asserts a cause and the package's own
  repro artifacts or control runs point elsewhere, the artifacts
  decide. A confident wrong diagnosis is still wrong when a maintainer
  shares it.
- **An honestly scoped-down plan is a complete plan.** Deferring part
  of a multi-part issue, with the deferral stated and reasoned, is
  bounded work, not an incomplete answer. Judge what the plan commits
  to, not what it chose to leave alone.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `cause-grounded` | The plan's stated cause (its diagnosis or cause sentences, wherever they sit) read against the `## Repro evidence` block's artifacts, control runs, and `Actual:` line. Live: the plan's cause read against the student's posted repro comment. | Pass if the behavior the stated cause predicts is the behavior the repro evidence actually shows, and no artifact or control run in that evidence rules the cause out. Fail if the repro evidence contradicts the stated cause — the blamed component is exercised and behaves correctly in a control run, the failure is already present in the evidence before the blamed step runs, or the evidence isolates a different variable as the one that flips the outcome — and fail if the plan names no cause at all. The issue body's or the thread's own stated cause does not rescue a diagnosis the package's evidence rules out; adopting a confident wrong cause from the thread is still a fail. | required |
| `scope-bounded` | The plan's scope or change statement plus its full list of proposed changes (the numbered approach steps, the files-and-areas list), read against the one behavior the issue reports. | Pass if every change the plan proposes is needed to make the reported behavior correct, or is a test, a doc line, or a follow-up note for it. An explicit not-in-scope line is strong evidence but is not required to pass, and a plan that deliberately defers part of the issue with a stated reason passes. Fail if the plan carries work the issue did not ask for alongside the fix: a library or dependency migration, a rewrite or redesign of the surrounding component, a new user-facing option, setting, or UI surface, a second reported bug folded in, a retry or abstraction framework, or a CI/tooling change. A correct direct fix buried inside that larger program still fails; the fail is the bundle, not the fix. | required |
| `approach-chosen` | The plan's approach and its files-or-areas statement: what will be edited, and what will be done to it. | Pass if the plan names a specific code location — a file, module, component, or function — AND commits to one approach a stranger could start executing today without asking the author which way to go. A location pinned to a named file or subsystem with the exact function left to confirm during implementation passes, as long as the approach itself is chosen. Fail if the first real step is to investigate, profile, bisect, or work out which layer is responsible; if no file, module, or component is named anywhere; or if a choice between stated alternatives is deferred to build time ("gocui? tcell? not sure", "upstream or vendored, whichever is easier", "try different protocols and see which behaves better"). | required |
| `test-plan-observable` | The plan's test plan, read against the `## Repro evidence` block's steps and artifacts. | Pass if the test plan names at least one concrete observation that would come out differently before and after the fix: a repro command re-run with a stated expected output, exit code, or rendered result; a named regression test over the issue's own input; or a stated behavior at a named step of the repro. Fail if the only stated test is a suite run with no fix-specific outcome ("run the full test suite and make sure nothing regresses"), or if the expected result is stated as a feeling or a generality ("should feel fast", "nothing else should feel broken", "scrolling should work correctly afterwards"). A suite run is fine *alongside* a fix-specific observation; it fails only when it is the whole plan. | required |
| `comment-engages-thread` | The candidate plan comment read against the `## Thread highlights` section (live: the issue's comment thread), with attention to comments from an OWNER, MEMBER, COLLABORATOR, or CONTRIBUTOR. | Pass by default when the thread carries no direction for a contributor: no comments, or only other users confirming the symptom. When someone with a repo role has named a culprit, proposed or rejected an approach, posted a patch or an open PR, or asked contributors for something, pass if the comment follows that direction, builds on it, engages the named PR, or states a reason for taking a different route. Fail if the comment proposes a direction the thread already rejected, or proposes its own while leaving an owner's isolated culprit, posted patch, or direct request unmentioned. The plan body engaging the thread does not rescue a comment that does not: this check reads the comment, because the comment is what the thread sees. | required |
| `policy-disclosure` | The `## Repo facts` contribution-policy line (live: CONTRIBUTING.md, AI_POLICY.md, and the repo's templates) read against the candidate plan comment. Assume the package was AI-assisted: this is course work produced with AI tooling, so a disclosure requirement that applies is genuinely unmet when nothing discloses. | Pass by default, and read the policy's scope literally. Silence passes. A policy that welcomes generative AI, or asks the contributor to understand and take responsibility for what they submit, or scopes its disclosure ask to pull requests and code, asks nothing of a plan comment and passes. Where the policy instead asks that comments to maintainers be in the contributor's own words, pass if the comment is specific and first-person about this issue, and fail if it reads as generated boilerplate. Fail only when the repo's stated policy requires AI use to be disclosed in contributions that include issue comments and the comment names no assistance. | required |
| `unknowns-marked` | The plan's risk, unknown, and open-question sentences read against what it states as settled. | Pass if anything the plan has not actually verified is marked as a guess, an unknown, or a thing to confirm during implementation, rather than asserted. A plan with genuinely nothing open ("Risk: none identified beyond ...") passes. Reported for the author's benefit; never changes the verdict. | preferred |

## Verdict rule

`accept` if every `required` check is `pass`. Any single `required`
check graded `fail` makes the verdict `reject`. `unclear` counts as
`fail`: a plan whose readiness cannot be established from what the
drafts contain is a plan that is not ready to build from. `preferred`
checks are reported with their grade and evidence but never change the
verdict.

There is no partial credit and no third verdict. A plan that is right
about the cause, bounded, executable, and decisively testable, but
whose comment walks past the owner's direction or omits a disclosure
the repo requires, is `reject`: the comment is the half that gets
posted.
