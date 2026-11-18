# Toolkit Portfolio

Path: `beat-3-portfolio/toolkit-portfolio.md`

**Student:** LaxmiPrasanna

## Featured Tools

I chose these four tools because they span the full contribution arc — finding an issue,
reproducing it, planning the fix, and checking the PR — and because each one has a moment
where my own judgment rule either caused a problem or fixed one. The issue-scout and
repro-check both changed visibly through use: the scout's rubric went from 15/20 to 20/20
after a targeted rewrite, and the repro-check's design gap became visible when it couldn't
handle a false-positive finding. The voice-guard and plan-check are featured because they
each had a specific failure I can point to and date.

---

**issue-scout**

**What it is, and the judgment hole I wrote:** The issue-scout runs a structured search for
open source issues that match a contributor profile and scores each candidate against eight
checks: maintainer-alive, repo-not-archived, recent-push, not-claimed, scope-bounded,
ai-policy, good-first-issue, and maintainer-responsive. The judgment hole is the rubric —
specifically the scope-bounded check, which decides whether an issue is one bounded change
or an umbrella. That judgment cannot be automated away; it requires a human-readable rule
precise enough that the model applies it the same way every time.

**How it evolved, dated:**

- Sep 20, 2026: Built the initial scout with four search queries and the eight-check rubric.
  First full eval run: 15/20. The scope-bounded check misfired in both directions — wrongly
  rejected three issues with detailed specs (read "multiple files" as an umbrella), wrongly
  accepted two bot-filed issues (didn't check the opener's username).
- Sep 20, 2026 (same session): Rewrote scope-bounded with six explicit fail conditions and
  an explicit pass condition. Added condition (6): fail if the issue was opened by a bot
  (username ending in `[bot]`). Ran `--only issue-20` to confirm the bot-filed catch didn't
  disturb the other 19: 1/1. Full run: 20/20.
- Oct 2026: Ran the same scout on real repos including kubestellar/console. It ranked
  kubestellar/console#24439 first on fit: TypeScript/React match, `help wanted` and
  `ai-fix-requested` labels, explicit AI policy pass, scope bounded to named files.

**Evidence:**

Before rewrite — scope-bounded check:

> Pass if the issue describes one self-contained change.

After rewrite:

> Fail if: (1) the issue is explicitly a tracking list or umbrella with sub-items meant to
> be split into separate PRs, (2) the thread shows an unresolved design debate with no
> maintainer settling it, (3) it is a pure support/usage question, (4) there are 2 or more
> closed unmerged PRs indicating repeated abandoned attempts, (5) it is a vague feature wish
> with no spec, or (6) the issue was opened by a bot (username ending in `[bot]`). Pass if:
> the issue describes one bounded change even if it touches multiple files, and even if it
> lists implementation suggestions.

Eval run result, committed Sep 20, 2026:

```
agreement: 20/20 scored items  (bar: 18/20: PASS)
categories: clear-accept 8/8  scope-creep 0/0  bot-filed 1/1  ...
```

---

**repro-check**

**What it is, and the judgment hole I wrote:** The repro-check structures how I document a
bug reproduction: environment setup, steps to reproduce, observed result, two control runs
that isolate the trigger. The judgment hole is the evidence-guide — my rules for what counts
as proof that a bug reproduces or doesn't. I wrote five verdict checks (artifact-shows-the-issue,
control-or-contrast, environment-stated, followable-steps, policy-respected) and the rules
for when each passes or fails.

**How it evolved, dated:**

- Oct 2026: Built the repro-check and ran it against the pathreview practice repo. First
  calibration run agreed 4/4 on gold labels; full run agreed 20/20 on first attempt.
- Oct 19, 2026: Ran the repro-check framework on the kubestellar accessibility issue.
  Found its main design gap: the rubric has no path for "confirmed not broken — false
  positive." The evidence-guide expects a traceback or an observable failure. A file audit
  proving nothing was wrong produces none of that. Had to adapt the evidence format manually.

**Evidence:**

The repro-check ran on pkg-20 (ghostty-org/ghostty#13604), the eval set's single
`disclosure` package. Nine of ten checks passed it. The single failure:

```
policy-respected — fail:
"AI_POLICY.md requires disclosing AI use (tool + extent) in all contributions
including issue comments; neither comment discloses it."
```

My rubric read it that way because `policy-respected` is `required` and my verdict rule is
"any single required check graded fail makes the verdict reject." That is deliberate — a
proof-only rubric would have accepted this package because every evidence signal is clean.
The check caught it because the rule is specific: "assume the package was AI-assisted" and
"read the policy's scope literally."

---

**voice-guard**

**What it is, and the judgment hole I wrote:** The voice-guard is a Claude hook that checks
a draft upstream comment against my voice-guide.md before I post it. The voice-guide has
five writing rules, a "things I never post" list, and an Upstream section with specific
rules for claims, questions, and pings. The judgment hole is the voice-guide itself —
I wrote the rules that define what my own upstream voice should be.

**How it evolved, dated:**

- Oct 2026: Built the voice-guide with the five core rules and the Upstream section.
  Added the kubestellar-specific pings rule after reading the repo's PR-TRIAGE-SLA.md.
- Oct 24, 2026: Ran the guard on my claim draft. Failed twice with "the claude CLI is not
  on PATH." Traced the issue to the shell PATH in the hook environment. Fixed by running
  from a terminal where PATH was set correctly. The guide itself did not change — it passed
  on the third run.
- Oct 25, 2026: Posted the saved claim draft on a closed issue without running the guard
  first. The guard would have caught the voice rule "do not post a direction into a thread
  whose maintainer has already resolved it." The mistake is recorded in guard-log.md.

**Evidence:**

guard-log.md entries, Oct 24, 2026:

```
2026-10-24 18:47 [manual] stop: checker failed (the claude CLI is not on PATH)
  | target: C:\Users\rlpra\AppData\Local\Temp\test-claim.md
2026-10-24 18:48 [manual] stop: checker failed (the claude CLI is not on PATH)
  | target: C:\Users\rlpra\AppData\Local\Temp\test-claim.md
```

The mistake on Oct 25 — posting on the closed thread — is the most concrete evidence of
what the guard is for. When it ran, it passed the claim. When I skipped it, the comment
violated the upstream rule the guide was written to prevent.

---

**plan-check**

**What it is, and the judgment hole I wrote:** The plan-check evaluates an upstream plan
comment against five required checks: cause-grounded, scope-bounded, approach-chosen,
test-plan-observable, and comment-engages-thread. The judgment hole is the
comment-engages-thread check — the rule that decides whether a plan comment is aware of
what the maintainer has already said in the thread. I wrote this check specifically because
the eval set had a package (pkg-04) where every plan check passed but the comment was
oblivious to an owner's posted patch.

**How it evolved, dated:**

- Oct 2026: Built the plan-check with the five required checks. First six-package smoke run:
  6/6. Full run: 20/20 on first attempt — no revise loop needed.
- The comment-engages-thread check was the design decision that required the most
  deliberate wording. Wrote four specific clauses: role filter (OWNER/MEMBER/COLLABORATOR
  only), pass-by-default (silence passes), "or states a reason for taking a different route"
  (disagreement with evidence is not a fail), and "the plan body engaging the thread does
  not rescue a comment that does not" (the check reads the comment, not the plan).

**Evidence:**

pkg-04 (junegunn/fzf#4260) — comment-engages-thread check output:

```
cause-grounded    — pass
scope-bounded     — pass
approach-chosen   — pass
test-plan-observable — pass
comment-engages-thread — fail:
  "junegunn (OWNER) named the culprit in src/tui/light_windows.go and posted a
  patched binary (commit 8916cbc); the comment proposes a documentation-only plan
  and never mentions either."
verdict: reject
```

Four required checks passed on the merits. One failed because the comment was written as
if the thread were empty. The check caught it; a rubric that only grades the plan body
would have accepted it.

---

## Inventory

- **repro-check** (featured above): structures bug reproduction evidence; ran on pathreview practice set (20/20) and kubestellar false-positive audit.
- **pr-precheck**: grades a pull request against five checks (cause-grounded, scope-bounded, plan-fidelity, test-evidence, standards-met); ran on pathreview PR set, 20/20 on full eval run.
- **pr-monitor**: reads an open PR's checks, reviews, and comments and drafts the next move; installed Oct 2026, playbook written against kubestellar merge history; ran `--state` only since no PR was opened.
- **ship-gate**: pre-push manifest of quality checks to run before opening a PR; drafted for the kubestellar ARIA-labels branch; never ran against a real push since the issue was closed first.

---

## The Arc

**Beat 1**
Date: September 20, 2026
Friction: The issue-scout's scope-bounded check misfired in both directions on the first eval run — 15/20. It rejected three good issues for having detailed specs and accepted two bot-filed issues because it didn't check the opener's username. I noticed because the eval harness showed the category breakdown: `scope-bounded` was the common failure reason across all five misses.
Change: Rewrote scope-bounded with six explicit fail conditions and an explicit pass condition. Added condition (6) specifically for bot-filed issues.
Result: Full run scored 20/20. The bot-filed catch (`--only issue-20`: 1/1) confirmed the fix didn't disturb the other 19 packages. The same scout then correctly ranked kubestellar/console#24439 first on a real repo search.

**Beat 2**
Date: October 2026
Friction: Ran the voice-guard on my claim draft and it stopped twice with "the claude CLI is not on PATH." The hook calls the claude binary, but the shell environment the hook ran in didn't have it. I noticed because the guard-log showed two consecutive stop entries with the same error.
Change: Fixed by running the guard from a terminal where PATH was correctly set. Did not change the voice-guide itself — the rules held up. Filed a note that the hook needs PATH resolution at install time.
Result: Guard passed on the third run. The claim comment was posted. The PATH fix is noted but not yet implemented in the hook.

**Beat 3**
Date: October 19, 2026
Friction: Posted my claim on kubestellar/console#24439 the day the issue was filed. Within 40 minutes the repo's hive bot had verified every flagged element, confirmed all ARIA labels were already present, and closed the issue as a false positive. I found out when I went to audit the component files myself.
Change: Adapted the repro-check evidence format to fit a "confirmed not broken" finding — file audit instead of traceback. Documented the gap: none of the rubrics have a first-class false-positive verdict path.
Result: Issue confirmed closed. No PR opened. Documented the gap in each tool's Toolkit block for future revision.

**Beat 4**
Date: October 25, 2026
Friction: Posted my saved claim draft on the closed issue without checking the thread status first. The comment reads as though the issue is still open. The voice-guide explicitly says not to post a direction into a thread whose maintainer has already resolved it — I violated it by skipping the guard.
Change: Added an explicit pre-post status check to my upstream workflow: always re-read the issue's current state before posting any comment, not just the first one.
Result: The mistake is recorded. The comment is still on the thread. No further action taken; the issue is closed.

---

## Limitations and What's Next

The toolkit as a whole assumes a contribution arc that reaches a pull request. Every rubric
has an accept path and a reject path. None of them has a path for a contribution that ends
at "not needed" — a false positive, a duplicate, or an issue that closes before the PR
stage. That gap forced me to adapt the evidence format at every stage of the kubestellar
contribution, and the adaptation was manual each time. The fix is a third verdict in each
rubric: `cannot-reproduce:false-positive` for repro-check, `not-needed:already-fixed` for
plan-check, with its own evidence shape defined in the evidence-guide.

The voice-guard's PATH dependency is a fragile integration point. Two of its three recorded
runs failed because the claude CLI wasn't on the shell PATH in the hook environment. A
safety check that fails to run is no check at all. The fix is to resolve the PATH at install
time or fall back to a local model when the CLI isn't available.

The pr-monitor was the tool I had the least real data on. I wrote the playbook against
historical merge times sampled from other contributors' PRs, not from my own thread. The
staleness threshold (14 days) is a reasoned estimate, not a confirmed number. The monitor
needs at least one live round-trip with a maintainer before the playbook is calibrated.

What I'd build next: a bot-triage check in the issue-scout — one check that looks at the
filing bot's history in the same repo (how often does it self-close its own issues, and how
quickly?) before committing to a full contribution workflow. The hive bot on kubestellar
closes its own issues within hours if no human PR appears. That pattern is visible in the
issue history. One check in the scout rubric would have flagged it and saved the full
workflow investment.

**Tool limits.** One line per featured tool, in the same order as the entries above.

issue-scout: Does not check whether the filing bot on a repo typically self-closes its own issues — the pattern that caused the false-positive miss on kubestellar/console#24439.
repro-check: Has no first-class verdict path for a false positive; forces the contributor to adapt the evidence format manually when nothing is broken.
voice-guard: PATH dependency makes the hook unreliable in shell environments where the claude CLI isn't on PATH; failed two of three runs recorded in guard-log.md.
plan-check: Cannot read a live issue thread in the eval harness — the comment-engages-thread check runs against a curated excerpt, so a maintainer's direction buried deep in a long thread can be missed.
