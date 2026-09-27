# Rubric: is this reproduction package ready to post?

Every check below judges the outcome, not the write-up's shape. Length,
headings, tables, bullet style, tone, and word count never decide a
check. A four-line report that shows the thing can pass every check; a
polished report with a nice summary table can fail most of them.

Two standing rules for every check:

- An honest cannot-reproduce is a normal, passing outcome. A report
  whose result is "I tried and it did not happen for me" is graded on
  whether it shows the attempt and names what differed, never on
  whether the bug appeared.
- Grade what the package contains and quotes. Work the author did but
  did not show is not evidence.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `claim-specific` | The claim comment alone (eval bundle: "Candidate claim comment"; live: the draft claim file), read against the issue's title and body. | Pass if the claim names something only a reader of THIS issue could name — the specific symptom, version, file, function, flag, error string, or the thread comment it is answering — AND states a concrete next action the author intends to take. Fail if it would read the same pasted under any other issue (`+1`, `same here`, `assign me`, `great project`, `any updates?`) or if its only content is interest in the issue. Fail regardless of how the repro report reads: this check never looks at the report. | required |
| `claim-promises-only-work` | The claim comment alone, read against what the author can actually control. | Pass if the author promises only their own investigation, testing, or reporting back. Fail if the claim guarantees a fix, promises a deadline or delivery date ("within 2 days", "by the weekend"), demands the issue be reserved or assigned to them as a condition, or asserts a completed diagnosis the report does not back. Asking to be assigned is fine; guaranteeing an outcome is not. | required |
| `env-recorded` | The repro report's environment record (eval bundle: the "Environment:" line or block, or wherever the report states its versions and platform; live: the draft repro file), read against the state the issue's behavior depends on. | Pass if the report names the tool's version AND the operating system or platform, plus any further state the issue itself makes decisive (the driver, shell, build profile, browser, installation method, or config the issue names as part of the trigger). Fail if there is no environment record at all, or if a piece of state the issue names as decisive is absent. A record spread through the prose rather than a labelled block still passes: this check reads for the facts, not for a heading. | required |
| `env-matches-target` | The environment record read against the version, platform, and code state the issue is about (the issue's stated version, the thread's confirmations, or the repo-facts block's latest release). | Pass if the environment tested is the one the issue is about, OR the report states the difference in its own words (for example "filed against 13.0.0, unchanged on 15.2.0", or "the report is macOS + fish; I ran Linux + zsh"). Fail if the report tests a different version, platform, or build than the issue targets and never says so — an unacknowledged deviation makes the artifact evidence about a different world. | required |
| `steps-rerunnable` | The repro report's steps and the inputs they name, read as a stranger with a clean machine would read them. | Pass if a stranger could obtain the starting state and reach the trigger from what the package gives them: the commands, input files, config, or settings are either shown in the report, described precisely enough to recreate, or pointed at something publicly available in the issue, the thread, or the repo. Fail if any step depends on something the reader cannot get (a private repository, an unshared config, an internal build, "our setup"), or if the step that triggers the bug is missing or left as an instruction to figure out. | required |
| `artifact-present` | The repro report's artifacts: command output, a log excerpt, a rendered result, a screenshot description, a test run, a transcript — produced by the author's own attempt. | Pass if the report contains at least one artifact the author's own run produced. Fail if the report is assertion only: confidence, a diagnosis, a root-cause narrative, a restatement of the issue, or a count of how many times it happened, with nothing shown. "It reproduces every time" is a claim, not an artifact. | required |
| `artifact-shows-the-issue` | The artifact read line by line against the behavior the issue describes: the specific error, exit code, output, or failure mode named in the issue body or settled by a maintainer in the thread. | Pass if the artifact shows the behavior the issue describes, on the trigger the issue names. Also pass if the report's stated result is a cannot-reproduce AND its artifact shows what the author's attempt actually produced instead. Fail if the artifact shows an adjacent outcome and the report reads it as the reported one — a different error class, a graceful failure where a panic or crash was reported, a different exit code, a different command or syntax than the trigger the issue names, or output that merely shows the tool ran. Compare the artifact to the issue yourself; do not take the report's narration of its own output as the finding. | required |
| `outcome-honest` | The report's conclusion sentences ("Expected", "Actual", the summary, any root-cause statement) read against what its own artifacts show. | Pass if every conclusion is supported by something shown, and anything the author inferred, assumed, did not test, or could not trigger is marked as such. A cannot-reproduce that names what differed and what a triggering setup would likely need passes fully. Fail if the report asserts more than it shows: a root cause "verified" with no transcript, a crash "confirmed" by output where the program survived, certainty language ("conclusively", "100%", "guaranteed reproducible", "on two machines") doing the work an artifact should do, or an "Expected"/"Actual" pair that does not match the run above it. | required |
| `policy-respected` | The repo-facts block's contribution-policy line (live: CONTRIBUTING.md, AI_POLICY.md, and the bug-report template in the repo), read against both comments. Assume the package was AI-assisted: these comments are produced in a course that uses AI tooling, so treat any disclosure requirement as one that applies. | Pass by default: silence passes, and a policy that only welcomes AI use, asks the contributor to understand and take responsibility, or restricts AI use in pull requests and code, needs nothing in an issue comment. Fail only when the repo's stated policy requires disclosing AI use in contributions that include issue comments and neither comment discloses it. Where the policy instead requires that comments be in the contributor's own words, pass if the comments are specific and first-person rather than generated-sounding boilerplate, and fail if they are not. Read the policy's scope literally: a disclosure requirement written for pull requests only does not bind an issue comment. | required |
| `control-or-contrast` | The repro report, read for a second run that isolates the trigger: the same command without the triggering flag, input, or setting; a passing case next to the failing one; a before/after. | Pass if the report shows a contrasting run that isolates what makes the difference. Never changes the verdict; a single faithful failing run is enough to post. | preferred |

Claim-side checks, for a claim-only draft in live mode: `claim-specific`,
`claim-promises-only-work`, and `policy-respected` (the claim comment
alone against the repo's policy). Every other check reads the repro
report; on a claim-only draft, grade those `unclear` with evidence
`not yet applicable: claim-only draft` and leave them out of the
verdict rule.

## Verdict rule

`accept` if every applicable `required` check is `pass`. Any single
`required` check graded `fail` makes the verdict `reject`. `unclear`
counts as `fail`, with one exception: a check marked
`not yet applicable: claim-only draft` in live mode is excluded from
the verdict entirely rather than counted as a fail. `preferred` checks
are reported but never change the verdict.
