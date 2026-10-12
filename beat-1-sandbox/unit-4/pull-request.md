# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/[YOUR_PR_NUMBER]

**Branch**

fix/68-keyword-searcher-zero-division

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

20/20 (agreement: 20/20 scored items)

**Package analysis**

Package: `pkg-02` (Textualize/rich#4208)

Gold label: `accept`

My rubric verdict: `accept`

Reasoning: The PR matches the plan exactly — all changed files fall within the scope (`rich/console.py` and `tests/test_console.py`), with a CHANGELOG.md entry as required. The test evidence is decisive: the issue's script is re-run before and after showing `export_text()` remains populated after a failed save (before: empty, after: 'IMPORTANT OUTPUT\n'), and pytest passes. The diff is clean with no debug output or dead code; commits are clear ('console: clear recorded output only after save_* writes succeed'). The template sections are all filled substantively (Type of changes: Bug fix, CHANGELOG entry present). This is a straightforward fix with proper evidence and template compliance.

**Check rationale**

Check quoted from `rubric.md`:

"Plan fidelity | The diff and commit list, read against the plan's scope and boundary, and the description's claims about what changed | Every changed file falls within the plan's stated scope or explicitly noted in a deviation; the diff does not silently add unscoped work; if the description claims the PR "implements the plan exactly" or equivalently, the diff makes exactly what the plan describes and no more | required"

Why this check: The gold labels show that silent drift (scope mismatches) is one of four failure families. This check directly detects it by comparing changed files against the plan's boundary and catching descriptions that claim fidelity but have unscoped additions. It catches both directions: PRs that do more than planned, or less without noting the deferral. The evidence source (diff against plan scope) is concrete and traceable, not subjective. Most of the rejected packages failed on this check because they either added unscoped work (new config options, rewritten functions) while claiming to "implement exactly", or made smaller changes than planned without disclosure.

**Trade-offs**

The tool scores all 20 packages correctly with perfect category distribution (7/7 clear-accept, 4/4 not-tested, 4/4 silent-drift, 2/2 standards-wall, 3/3 unreviewable). No trade-offs needed: every category is matched and the bar is exceeded. A key trade-off that could have arisen but was resolved through evidence-guide clarity: initially the tool rejected pkg-05 and pkg-13 because it required separate before/after evidence for every scenario a plan names. Revising the evidence-guide to clarify that controls (unchanged cases) are verified when the repo's test suite passes, without requiring duplicate before/after evidence, fixed these false negatives. This resolved without loosening the core checks.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
