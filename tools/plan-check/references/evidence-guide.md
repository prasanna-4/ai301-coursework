# Evidence guide: where evidence lives in a plan package

The map the rubric's checks read with. For each family: the exact
places to look, then the observable condition that decides it.

A note that applies everywhere below. In an eval bundle the sections
are named: `## Repo facts`, `## Issue`, `## Thread highlights`,
`## Repro evidence`, `## Candidate plan`, `## Candidate plan comment`.
The bundle is the whole world; nothing is fetched. In live mode the
same six things live on GitHub and in the working directory: the
repo's CONTRIBUTING.md / AI_POLICY.md / `.github/` templates, the
issue body, the issue's comment thread, the student's own posted repro
comment on that issue (or the house repro pack as quoted in the
drafts), `plan.md`, and `comment.md`.

One structural warning for this week specifically. A plan package has
two halves that are graded against different things: the plan is
graded against the repro evidence, and the comment is graded against
the thread and the repo's rules. They fail independently. Do not read
the plan's good diagnosis as evidence about the comment, and do not
read a well-mannered comment as evidence about the plan.

## Diagnosis and grounding

**Where it lives.** The plan's cause sits wherever the plan states it,
not under a particular heading: a `Diagnosis:` line, a `Cause:` line,
or the first sentence of an unlabelled plan ("The mouse handling
clearly changed somewhere between 0.61 and 0.62"). The evidence it
must answer to is the `## Repro evidence` block, and inside that
block, in descending order of weight: the **control runs** (the lines
labelled `Control`, or a second run that changes one variable), the
**artifacts** (fenced output, tracebacks, exit codes, measured
timings), and the **`Actual:` line**, which usually states in words
which variable flips the outcome. The `## Issue` section's own "root
cause per the report" line and any thread diagnosis are context, not
evidence: they are claims, and the repro block is what was run.

**What good looks like.** The stated cause predicts the behavior the
artifacts actually show, and survives every control. Walk the controls
one at a time and ask what each one eliminates — that is what a
control is for, and in these packages it is almost always the deciding
evidence. The grounded form says so out loud: "Both controls in the
repro fit: no background, no subtraction; width 2, no overshoot."

The failure is a diagnosis the package's own evidence has already
ruled out, and it is usually confident and well written, because the
author took it from the issue or from a maintainer. Three shapes to
watch for:

- **The control exonerates the blamed component.** The plan blames the
  request-item tokenizer, and a control run with the same items and no
  `-v` flag parses them fine; the tokenizer is not what changed.
- **The failure predates the blamed step.** The plan blames a cast
  applied after a read, and the evidence shows the values already
  wrong in the table before any cast runs. Nothing downstream can be
  the cause of something already upstream.
- **The evidence isolates a different variable.** A timing matrix
  shows the cost with the blamed component out of the loop entirely,
  and shows the symptom gone when a different setting is changed. The
  variable the evidence flips is the cause; the one the thread talked
  about is not.

A diagnosis that is merely incomplete is not the failure here. "The
responses arrive as ordinary input and are echoed" with the exact
function left to trace is grounded if the controls fit it.

## Scope

**Where it lives.** The plan's `Scope:`, `Change:`, or `In scope:`
sentence, its `Not in scope:` / deferral sentence, its numbered
approach or proposed-changes list, and its files-and-areas list. All
four are part of the scope statement; the numbered list is the one
that tells the truth, because a plan can write "one bounded change"
above five fronts of work. Read it against the `## Issue` section's one
reported behavior.

**What good looks like.** Every item on the list is load-bearing for
the reported behavior, or is a test, a doc note, or a cross-reference
for it. The bounded plan reads small even when the bug is deep:
"saturating clamps at both subtraction sites in `src/printer.rs`, a
width-1 regression test, nothing beyond the abort." A stated
not-in-scope line is the strongest single signal, and the best ones
name the tempting adjacent work and refuse it by name ("Not in scope:
the tombstone rework discussed in the thread; it is the better
long-term shape but a larger change").

A drive-by rewrite looks like a list where the fix is item 1 and items
2 through 5 are work the issue never asked for: a dependency migration
("while touching the network stack, replace node-fetch with undici"),
a redesign of the component around the fix, a new settings field or
user-facing option, a second reported bug folded in "while in the
area", a retry or abstraction framework, a CI matrix. The tell is the
phrase *while we are here* in any of its dialects, and the giveaway
that it is still a fail is that item 1 is often exactly right. The
bundle is what fails.

Deferral is the opposite of scope creep and reads similarly at a
glance, so separate them deliberately: deferral removes work from this
change and says why ("I cannot test Windows, so I am explicitly
leaving the Windows variant out and will flag the shared fix site in
the PR"). That is a bounded plan, not a partial one.

## Executability

**Where it lives.** The plan's approach or numbered steps, plus its
`Files:` or areas list. In live mode, the same two parts of `plan.md`.

**What good looks like.** Two things must both be present: a named
code location, and a chosen approach for it. A stranger holding the
package should be able to open a file and begin, without needing to
ask the author which direction was intended. The location can be a
file (`src/jv_aux.c` (delpaths_sorted)), a module, or a named
subsystem ("the client attach/reattach path in `zellij-server`'s
client connection handling"); precision to the line is not required,
and "exact functions to be pinned in the PR after tracing the query
issuance with debug logs" is a healthy honest edge on a plan that has
already chosen what to do.

The unbuildable plan defers the decision rather than the detail. Its
first step is an investigation ("Investigate how lazygit reads mouse
events (gocui? tcell? not sure which layer is responsible)"), or an
experiment ("try different mouse protocols and see if one behaves
better"), or a profiling pass with no chosen fix after it. Sometimes
it names no file anywhere. Sometimes it states alternatives and picks
neither: "recover() somewhere", "fix upstream or vendored, whichever
is easier". Each of those is a decision the author would have to make
before anyone could start, which means the plan has not been made yet
— it has been scheduled.

## Test plan

**Where it lives.** The plan's `Test plan:` sentence or section, read
against the `## Repro evidence` block's numbered steps and artifacts.
The best test plans point straight back at those steps, so read them
side by side: the repro is the before, and the test plan should be the
same thing with a stated after.

**What good looks like.** At least one named observation that would
come out differently before and after the fix. In practice it takes
one of three forms: the repro command re-run with a stated expected
result ("re-run the repro command, expect drawn output and exit 0";
"at step 3 the color must flip without leaving the view"), a named
regression test over the issue's own input ("add the issue's three
cases to `tests/jq.test`"), or a stated behavior at a named step ("5
consecutive SSH reattach cycles with no rgb strings in any pane").
Naming the control runs too — "the regex-mode control stays
unchanged" — is what a decisive test plan has that a merely adequate
one does not.

The vague test plan proves nothing observable. Its two shapes:

- **The suite stands in for the test.** "Run the full test suite
  (`cargo test --workspace`) and make sure nothing regresses." The
  suite passes today, before the fix, so it cannot witness the fix.
  This is the most dangerous shape because it looks rigorous, and it
  can sit under an otherwise solid bounded plan. A suite run alongside
  a fix-specific observation is fine; a suite run *as* the test plan
  is not.
- **The outcome is a feeling.** "Should feel fast", "nothing else
  should feel broken", "scrolling should work correctly afterwards".
  Nobody can run that and disagree with the author.

## Honesty

**Where it lives.** The plan's `Risk:`, open-question, and unknown
sentences, and the adjectives attached to its claims ("confirmed",
"verified", "clearly"). After a build, the `## Deviations` section at
the end of `plan.md` is where the same honesty gets recorded for what
actually happened.

**What good looks like.** The seams are marked. What the author has
not verified is labelled as a guess or an open question rather than
asserted: "I have not yet measured the per-print cost of the
generation comparison"; "I have not yet verified which layer clamps
the viewport, so the exact fix site may move one level during
implementation (named as an unknown, not a certainty)". A plan with
genuinely nothing open can say that too — "Risk: none identified
beyond one extra network request per channel per 24h" — and that is a
pass, not an evasion, when the change really is that small.

The failure is certainty doing an artifact's job: a cause called
"confirmed" that no control supports, a cost called negligible that
was never measured, an approach whose hard part is described as
mechanical. Length is not the signal — the longest and most polished
plans in a bad set are usually the overconfident ones, and a terse
plan can be perfectly honest.

Post-build, a deviation that exists only in the diff is the failure;
a deviation written under `## Deviations` with what changed and why is
honest work, and "nothing changed from the plan" written out in the
author's own words is a complete answer to that section.

## Comms

**Where it lives.** The `## Candidate plan comment`, read against two
separate things: the `## Thread highlights` section, and the
`## Repo facts` contribution-policy line. Live: the issue's comment
thread on GitHub, and CONTRIBUTING.md / AI_POLICY.md / the `.github/`
templates, plus `scope.md`'s house rules for course work.

Read the comment **alone**, without the plan beside it. The thread
sees only the comment. A plan that engages the maintainer's direction
in its scope section does not help a comment that does not mention it.

**What good looks like, against the thread.** First decide whether the
thread carries direction at all. The role tag after each name is the
filter: OWNER, MEMBER, COLLABORATOR, and CONTRIBUTOR comments can
carry direction; NONE comments are usually other users confirming the
symptom, and a thread of those (or a thread with no comments) asks
nothing of the comment, which then passes this family by default.

Direction takes four shapes, and all four bind the comment: a named
culprit ("This seems to be the culprit", pointing at
`src/tui/light_windows.go` lines 70-84), a proposed or rejected
approach ("recomputing `prev` at every use is too expensive for this
hot path; recompute only if the capacity changed"), a posted patch or
an open PR ("posted a patched test binary and asked the reporter to
test it"; "#3543 and #3548 are the concrete patches on the table"),
and a direct request to contributors. Thread-aware means the comment
takes a position on it: follows it ("My plan follows the direction
proposed here: a page generation counter..."), builds on it, engages
the PR and says how it will avoid duplicating it ("I have read PR
#2089, which takes the same route; if that lands first I will rebase
my tests onto it"), or departs from it with a stated reason.

The wall is a comment that proposes its own direction into a thread
that already has one, without a word about the one it is walking past
— an owner isolated the culprit and posted a patch, and the comment
announces a documentation plan instead, as though the thread were
empty. It usually reads politely and specifically, which is why it has
to be checked against the thread rather than on its own merits.

**What good looks like, against the policy.** Start from pass and read
the policy's scope literally, exactly as in week 2. Most repos say
nothing about AI, and silence passes. Three cases actually arise:

- **Nothing asked of comments.** A standard contribution guide; a
  policy that welcomes generative AI with the contributor responsible
  for understanding what they submit (conda); a policy that asks for a
  tool-and-extent statement in the pull request and states no
  disclosure ask for issue comments (fd). All pass with no disclosure
  line.
- **Own words required.** The repo asks that comments to maintainers
  be written by the human in their own voice, and AI-generated
  comments may be hidden (ripgrep). Satisfied by a specific,
  first-person comment about this issue, not by a disclosure line.
  Generated-sounding boilerplate fails it.
- **Disclosure required, comments included.** The repo requires all AI
  usage in any form to be disclosed, naming the tool and the extent,
  with AI-assisted issues and comments reviewed by a human before
  submission (ghostty). Then the comment must say so. Assume the
  package in front of you was AI-assisted — this is course work
  produced with AI tooling — so a missing disclosure is a real
  absence, not an inapplicable rule. This is the one case where an
  otherwise excellent, thread-following, bounded plan is still not
  ready to post, and it is easy to miss precisely because everything
  else about the package reads well.
