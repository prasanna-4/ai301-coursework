# Rubric: is this pull request ready to submit?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Plan fidelity | The diff and commit list, read against the plan's scope and boundary, and the description's claims about what changed | Every changed file falls within the plan's stated scope or explicitly noted in a deviation; the diff does not silently add unscoped work; if the description claims the PR "implements the plan exactly" or equivalently, the diff makes exactly what the plan describes and no more | required |
| Test evidence | The test-evidence section's before/after, read against the plan's test plan and repro scenario; also the repo's stated checks or CI suite run | For each scenario the plan names: evidence is shown (before/after transcript, passing test output, or unit tests passing). The plan's primary fix must have observable before/after shown. Controls (unchanged cases) are verified when the repo's stated test suite passes (which runs existing tests); separate before/after evidence is not required for controls. | required |
| Diff quality | The unified diff itself: changed code and structure, commits and commit messages | The fix is visible (the changed code directly addresses the stated problem); no unrelated hunks, debug output, commented-out code, dead code, pure formatting churn, or mechanical churn; commit messages are clear | required |
| Standards and comms | The repo facts' PR template asks and stated policy (AI disclosure if present); the description and PR text against those asks | Every required template section is filled with substantive content (not boilerplate or empty); if the repo states an AI-use policy, it is honored (disclosure present if required, or policy explicitly permits undisclosed AI use) | required |

## Verdict rule

Accept if every required check passes (a single fail holds). Unclear grades default to fail (an unverifiable claim is a failing one): if you cannot confirm that the plan's repro was actually run and the before/after shown, the test evidence check fails; if you cannot confirm from the diff whether a file belongs to the plan, it fails; if a template ask is visibly ignored or a required disclosure is absent, standards fails.
