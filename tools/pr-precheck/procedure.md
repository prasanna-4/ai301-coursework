# Procedure: how this tool grades a PR package

## Read order

Before grading any check, read the package in this order:

1. **Plan scope**: Start in the plan-context block. Note the stated scope (which files, which boundaries, which changes are in vs. out of scope). Record any deviation notes that permit exceptions to the scope.
2. **PR description**: Read the title and description. Note any claims about scope ("implements the plan exactly", "no changes beyond X", deferred work, etc.).
3. **Diff**: Read the unified diff carefully. List every changed file and map it against the plan's scope. Note the substance of each change (what problem it addresses).
4. **Test plan and evidence**: Review the plan's test plan (what must be verified). Then read the test-evidence section. Note whether the specific repro is run, what the before/after shows, and whether repo checks are shown passing.
5. **Repo facts**: Note the PR template's stated asks and any AI-use policy. Compare against the PR's title, description, and commits.

This order matters: you cannot judge plan fidelity without the scope first, and you cannot spot silent drift without comparing the description's claims against the actual diff.

## Evidence gathering

For each evidence family, gather these facts:

**Plan fidelity:**
- List the plan's stated scope (exact file names, boundary, what is excluded)
- List any deviation notes approved in the plan
- List every changed file in the diff
- Record any claim in the description about scope fidelity (e.g., "exactly as planned", "no API changes")
- For each changed file: does it fall in scope, or is there a deviation note explaining it? If not, flag as potential drift.

**Test evidence:**
- Record the plan's test plan verbatim (the specific repro, expected behavior, what checks must run)
- Record what the test-evidence section shows
- For each scenario the plan names: Is the before/after shown? Is the observable difference named (not just "pass" but the actual value or error)?
- Note whether repo-stated checks are shown running and passing

**Diff quality:**
- Scan the diff for unrelated hunks (file rewrites, config changes, structural refactoring outside the fix)
- Scan for debug output (console.log, print, eprintln, debug statements)
- Scan for dead code (allow(dead_code) annotations, commented-out functions, old implementations)
- Scan for pure formatting churn (indent changes with no code change, newline-only hunks)
- Scan commit messages: are they clear or vague ("wip", "fix", "cleanup")?

**Standards and comms:**
- List required sections from the repo template (e.g., "Closes #xxxx", "Type of change", "CHANGELOG entry", "AI disclosure")
- For each required section: is it filled with real content in the PR, or is it missing, empty, or boilerplate?
- If the repo states an AI-use policy: is it honored (disclosure present if required)?

## Check execution

Execute checks in rubric order. For each check:

1. Review the gathered evidence from its section above.
2. Apply the pass condition exactly as written in the rubric.
3. If evidence is genuinely absent (the package does not include test output, or the plan does not state a test plan), record that absence. An absent fact is not evidence: if the test-evidence section is empty and the rubric requires observable before/after, the evidence check fails.
4. Grade the check: pass, fail, or unclear.
   - **Pass**: The pass condition is met by the gathered evidence.
   - **Fail**: The pass condition is not met or the evidence is absent.
   - **Unclear**: You cannot determine from the package whether the condition is met (for example, a commit message is so terse you cannot tell if it is clear enough). When in doubt, default to fail.

## Verdict assembly

Assemble the verdict by applying the rubric's verdict rule:

- If any required check fails, the verdict is **reject**.
- If all required checks pass, the verdict is **accept**.
- Preferred checks never change the verdict (the rubric has no preferred checks).
- If the verdict is **reject**, quote the first failing required check in the output (that is, the check that appears earliest in the rubric's table).

All four checks are required in the rubric above. If all pass, accept. If any fail, reject on that check.
