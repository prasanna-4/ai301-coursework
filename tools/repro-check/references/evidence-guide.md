# Evidence guide: where proof lives in a reproduction package

The map the rubric's checks read with. For each family: the exact
places to look, then the observable condition that decides it.

A note that applies everywhere below. In an eval bundle the sections
are named: `## Repo facts`, `## Issue`, `## Thread highlights`,
`## Candidate claim comment`, `## Candidate repro report`. The bundle
is the whole world; nothing is fetched. In live mode the same five
things live on GitHub and in the working directory: the repo's
CONTRIBUTING.md / AI_POLICY.md / `.github/` bug-report template, the
issue body, the issue's comment thread, and the student's draft files.

## Environment

**Where it lives.** In a bundle: the repro report's opening
`Environment:` line or block, and anything version-shaped elsewhere in
the report (an install command, a `--version` transcript, a config
path). Read it against the issue's own environment statement: the
issue body's version line, the template fields the repo asks for (the
`## Repo facts` "bug reports:" line names them), and any thread comment
where someone confirms a version. In live mode: the draft repro file,
against the issue body's environment block and the repo's bug-report
template.

**What good looks like.** The tool's version and the OS or platform are
both stated, plus whatever further state the issue itself makes part of
the trigger — the driver on a driver-specific issue, the shell on a
shell-specific one, the build profile where debug and release behave
differently, the browser and its language order, the install method
where packaging is implicated. The facts may sit in prose rather than a
labelled block; an `Environment:` heading proves nothing on its own and
its absence disproves nothing.

Then read the record against the issue's target: either the versions
and platform match what the issue is about, or the report names the
difference out loud ("filed against 13.0.0, behavior unchanged on
15.2.0"; "the report is macOS + fish, I ran Linux + zsh; starship
version matches"). A named difference is a strength — it is what turns
"my run" into evidence about the issue. An unnamed difference is the
failure: nothing in the report tells the reader which world the
artifact came from, so the artifact cannot be weighed.

## Steps

**Where it lives.** In a bundle: the `Steps:` section of the repro
report, plus every command, file, and setting the steps name. In live
mode: the draft repro file, read alongside the issue's own reproduction
steps and any minimal reproduction linked in the thread.

**What good looks like.** Read from a cold start: a stranger on a clean
machine, holding only this comment, the issue, and the public repo.
They must be able to reach the starting state and then hit the trigger.
Every input is obtainable — pasted into the report, described exactly
enough to recreate ("a minimal `env.yml` with a valid `dependencies:`
list plus a `category:` section"), or pointed at something public (the
issue's own file contents, a playground link, a script quoted in the
thread). The trigger itself is the step that must be exact: the flag,
the syntax, the range, the key press, the setting. Reproductions whose
inputs cannot leave the author's machine — a private monorepo, an
internal config, a company build — are unusable as proof no matter how
faithfully they were run, and saying "I cannot share it" does not
repair that.

## Behavior shown

**Where it lives.** In a bundle: the fenced blocks in the repro report
— command output, log excerpts, tracebacks, rendered CSS, prompts,
exit codes — and any described screenshot. Read each one against the
issue's own artifacts: the error text, panic message, exit code,
wrong output, or failure mode quoted in the `## Issue` section, plus
anything a maintainer settled in `## Thread highlights` (an owner
naming the required flag, a member spelling out the correct output).
In live mode: the same blocks in the draft, against the issue body and
thread on GitHub.

**What good looks like.** The artifact shows the issue's behavior, on
the issue's trigger. Compare them yourself, token by token: the error
class, the message text, the exit code, the command and its exact
syntax. An adjacent artifact is the common failure and it is usually
well-dressed — a graceful `error: Invalid value` with exit 1 standing
in for a `capacity overflow` panic with exit 101; a compile error
standing in for a runtime path error; an old version's `ValueError`
standing in for the reported crash; garbled escape-sequence output
with the window still open standing in for a terminal crash; a colon
where the issue's syntax needs an equals sign. Weaker still is the
artifact that shows only that the tool started: a version banner, a
session list, "all three tabs are visible" — evidence of setup, not of
the bug.

A cannot-reproduce has artifacts too, and they matter just as much:
what the attempt actually produced (the markers in the log, the prompt
that still rendered) is the finding. Read those against the issue the
same way — they should show the author genuinely reaching the trigger
and getting something else.

## Honesty

**Where it lives.** Where the report's sentences meet its own blocks:
the `Expected:` and `Actual:` lines, any summary or conclusion, any
root-cause statement, and the adjectives around the artifacts
("confirmed", "verified", "guaranteed"). In live mode: the same lines
in the draft.

**What good looks like.** Every conclusion traces to something shown
above it, and the report marks the seams — what was inferred, what was
not tested, what the author could not trigger, which scenario of a
multi-part issue this report covers. The strongest form of this is the
honest negative: "I could not reproduce scenario 2", followed by the
attempt, the artifact, what differed from the report's conditions, and
what a triggering setup would likely need. That is a complete, postable
result.

The failure is the gap between the prose and the blocks. Watch for a
root cause "verified" with no transcript anywhere in the report; a
crash "confirmed" by output in which the program plainly survived;
certainty doing an artifact's job ("100% reproducible", "ran it ten
times", "on two separate machines", "conclusively demonstrates");
`Expected:` written backwards, describing the bug rather than the
correct behavior; and a conclusion that generalizes past the run
("confirms it on the Store release too") when only one build was
tested.

## Comms

**Where it lives.** The claim comment, read against the issue it is
posted under, and both comments read against the repo's stated rules.
In a bundle the rules are in `## Repo facts`: the "bug reports:" line
(what the template asks for) and the "contribution policy:" line, which
names CONTRIBUTING.md and any AI_POLICY.md and summarizes what they
require. In live mode: CONTRIBUTING.md, AI_POLICY.md, the `.github/`
templates, and — for course work — `scope.md`'s house rules, which
override the usual reading of a classmate's claim.

**What good looks like, in the claim.** It could only have been written
under this issue: it names the symptom, the version, the file or
function, the error string, or answers a specific comment in the thread.
It says what the author will do next, concretely. And it promises only
work the author controls — investigation, testing, reporting back.
Boilerplate is the opposite and is recognizable by substitution: if the
comment reads identically under any other issue, it fails, however warm
it is. So does the over-promise: a guaranteed fix, a delivery date,
"keep this issue reserved for me".

**What good looks like, against the policy.** Read the policy's actual
scope, and start from pass. Most repos say nothing about AI, and
silence passes. A policy that welcomes AI use, or asks the contributor
to understand and take responsibility for what they submit, or governs
pull requests and code, asks nothing of an issue comment. Two policies
do bind a comment, and they bind it differently:

- **Disclosure.** The repo requires AI use to be disclosed in
  contributions that include issue comments (ghostty's AI_POLICY.md is
  the type case: all AI usage in any form, stating the tool and the
  extent). Then the comment must say so, naming the assistance. Assume
  the package in front of you was AI-assisted — this is course work
  produced with AI tooling — so an absent disclosure is a real absence,
  not an inapplicable rule. Read the scope narrowly in the other
  direction too: fd's policy asks for a tool-and-extent statement in
  the pull request and explicitly asks nothing of issue comments, so an
  undisclosed issue comment there is fine.
- **Own words.** The repo requires comments to maintainers to be
  written by the human in their own voice (ripgrep's "Use of AI"
  section; AI-generated comments may be hidden). This is satisfied by
  a specific, first-person comment about this issue — not by a
  disclosure line. A generated-sounding comment fails it.

A comment can satisfy both at once: p5.js allows assistive AI with the
contributor responsible, and a claim that says which assistant helped,
that the author ran and verified every step, and what they understood,
is what a conditional policy passing looks like.
