# Building a PR-Readiness Toolkit: Eval Harnesses, Voice Guards, and What I Learned When the Issue Closed First

## Overview

This term I built a four-tool AI-assisted contribution workflow and used it to attempt a first open source contribution to [kubestellar/console](https://github.com/kubestellar/console). The tools — issue-scout, repro-check, plan-check, and pr-precheck — are evaluation harnesses that each take a candidate (an issue, a reproduction, a plan, or a PR) and score it against a rubric I wrote. I wrote the rubric, the search strategy, the voice guide, and the ship manifest; Claude ran the eval and drafted outputs. The contribution itself targeted [issue #24439](https://github.com/kubestellar/console/issues/24439), an accessibility scan flagging missing ARIA labels on dashboard buttons. The issue was closed as a false positive before I could open a PR — the repo's own automation verified all elements were already accessible within 40 minutes of the issue being filed. The contribution ended without a merged PR, but the toolkit ran on real data the whole way through.

## What happened

### Building the issue-scout (Unit 5)

The first tool I built was the issue-scout, which runs a structured search for open source issues that match a contributor profile and scores each candidate against eight checks: maintainer alive, repo not archived, recent push, not claimed, scope bounded, AI policy pass, good-first-issue label, and maintainer responsive. I wrote the rubric from scratch, then ran it against three candidates in the codepath/pathreview-ai301-fa26-s1 practice repo. The first run scored 15/20 — the `scope-bounded` check misfired in both directions, wrongly rejecting three detailed specs and wrongly accepting two bot-filed issues. Here is the check as it read before the fix:

> Pass if the issue describes one self-contained change.

That wording is too vague. The model applied it inconsistently, penalizing terse bodies and detailed ones unpredictably. I rewrote it to list explicit fail conditions:

> Fail if: (1) the issue is explicitly a tracking list or umbrella with sub-items meant to be split into separate PRs, (2) the thread shows an unresolved design debate with no maintainer settling it, (3) it is a pure support/usage question, (4) there are 2 or more closed unmerged PRs indicating repeated abandoned attempts, (5) it is a vague feature wish with no spec, or (6) the issue was opened by a bot (username ending in `[bot]`). Pass if: the issue describes one bounded change even if it touches multiple files, and even if it lists implementation suggestions — a detailed spec or a maintainer/collaborator filing it with a good-first-issue label is sufficient evidence of bounded scope.

After that rewrite, the full eval run scored 20/20. The bot-filed condition (6) was the decisive addition — it caught the one bot-filed issue (cursor[bot]) that the vague wording had accepted. I confirmed it by running `--only issue-20` before the full run: 1/1, with the prior 19 unaffected.

The same scout then ran on kubestellar/console and two other real repos. It ranked kubestellar/console#24439 first on fit: TypeScript/React match, `help wanted` and `ai-fix-requested` labels, explicit AI policy pass, and a scope bounded to named files in the dashboard component folder.

### Posting my claim and hitting the two-hour window (Unit 6)

Before posting my claim comment I ran it through the voice-guard — a tool I built as a Claude hook that checks a draft comment against my voice-guide.md. The voice-guide has five writing rules (paste output not summaries; name the edge of what you tested; promise the next step not the outcome; write issue-specific comments; credit classmates), a "things I never post" list, and an Upstream section with specific rules for claims and pings. The guard stopped twice during testing because the claude CLI wasn't on the shell PATH. Once I fixed the PATH, it passed.

My claim comment went up on Oct 19, 2026, the same day the issue was filed:

> I'm working on this as my first contribution. I'll add ARIA labels to the interactive elements listed in the spec. Plan to complete it within 3-5 days, starting with a local test setup.

The hive scanner bot had already started automated work at the same time (claim lapse: 2 hours). Within 40 minutes of the issue being filed, the bot posted its full verification and closed it:

> Verified every flagged location against main — all are already accessible; no code change needed: Buttons "possibly missing ARIA labels": all have visible text children, or an existing aria-label / title where icon-only. Images without alt: all 10 flagged `<img>` elements already have meaningful alt text. These are Auto-QA scanner false positives (regex-level detection can't see text children / existing attributes). Closing as not planned.

My own file audit confirmed this. The scanner's regex-level detection cannot read text children expressed as JSX children or translation-key calls — it only sees the opening tag. All flagged buttons were already accessible. No code change was needed.

### What the repro-check and plan-check revealed about tool design (Unit 7)

Running repro-check on a false positive forced me to adapt the tool's evidence format. Repro-check expects a reproducing failure — a command, a traceback, an observable before/after. A "confirmed not broken" finding produces none of that. I adapted by documenting the file audit as the evidence: open each flagged file, read the JSX, confirm text children or existing `aria-label`. Here is the kind of output the audit produced (from my read of `MissionActionPanel.tsx`):

```
// Lines 89, 99, 109, 119 — flagged by scanner
<button onClick={handleSnooze}>{t('actions.snooze')}</button>
<button onClick={handleDismiss}>{t('actions.dismiss')}</button>
```

The text child `{t('actions.snooze')}` is the button's accessible name. No `aria-label` attribute is needed. The scanner flagged it because it only saw `<button` on the line and didn't read forward to the closing tag.

This gap — the rubric has no "cannot reproduce: false positive" verdict path — is the clearest thing I would change in a future revision. I would add a third verdict alongside accept/reject: `cannot-reproduce:false-positive`, with its own evidence shape (file audit, what you read, why the original report was wrong).

The plan-check ran on the same non-fix conclusion. It produced a scope statement ("audit only, no code change") that matched the evidence. What it couldn't do was give me a natural place to record a contribution that ended without an implementation.

### Running the pr-monitor with no PR (Unit 8)

I installed the pr-monitor from the unit-8-starter materials and wrote my playbook.md with four sections:

- **Response rules**: open with the specific change requested, never with generic thanks
- **Staleness thresholds**: 14 days based on sampling 5 merged PRs in kubestellar/console (most waited 7–12 days for a first review)
- **Nudge etiquette**: ping the reviewer by name, reference the PR number, attach one new piece of evidence
- **Walk-away criteria**: close if no response in 30 days or if maintainer explicitly asks to withdraw

Running `monitor.py --state` correctly reported `NO PR YET` on my branch. Because no PR was open, the monitor had nothing to draft. The playbook is calibrated against the repo's actual merge history, but it was never tested against a live PR thread on this contribution.

### The mistake I recorded

On Oct 25 — six days after the issue was closed — I posted my saved claim draft as a new comment on the closed issue. The voice-guide says not to post a plan comment into a thread whose maintainer has already resolved it. I violated that rule because I didn't check the issue status before posting. That comment is still on the thread and reads as though the issue is still open. It's the clearest process failure in my record this term, and it's the kind of error that would make a maintainer less likely to trust a future comment from the same account.

## Limitations and what's next

The toolkit assumes a contribution arc that reaches a PR. Issue-scout, repro-check, plan-check, and pr-precheck each have rubric paths for accept and reject, but none has a first-class path for the contribution that ends at "not needed" or "false positive." That gap meant I had to adapt the evidence format at every stage of this contribution. The fix is a third verdict in each rubric — I would add it to repro-check first, since false positives surface earliest in the reproduction stage.

The voice-guard's PATH dependency is a fragile integration point. The guard's value depends on it running every time, but two of its three recorded runs failed due to the claude CLI not being on PATH. I would rewrite the check_voice.py hook to either resolve the PATH at install time or fall back to a local model so it always runs.

The pr-monitor was the tool I had the least real data on. The playbook is calibrated against historical merge times, but those numbers come from PRs by other contributors in a different context. The staleness threshold (14 days) is a guess with a methodology, not a number I confirmed against my own PR experience. I'd use the monitor on a future contribution and revise the playbook after seeing one live round-trip with a maintainer.

What I'd build next: a triage tool for auto-filed issues that checks whether the filing bot is likely to self-resolve (by looking at the bot's history in the same repo) before I invest a full contribution workflow. The hive bot on kubestellar/console closes issues it files within hours if no human contributor opens a PR. That pattern is visible in the issue history, and a one-check scout rubric could have flagged it.
