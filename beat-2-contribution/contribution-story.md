# Contribution: [Auto-QA] Interactive elements missing ARIA labels

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Prasanna
**GitHub Username:** prasanna-4
**Issue:** https://github.com/kubestellar/console/issues/24439
**Pull Request:** No PR opened — issue was closed as false positive before a fix was needed

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Built issue-scout skill with search queries and ranking filters; discovered query syntax errors and per-repo cap limitations**

I built the issue-scout skill by writing four search queries tailored to my fullstack TypeScript/React profile and applying ranking filters to find unclaimed, recently-updated issues. The first scan found two accepted issues, but revealed friction: Query 3's quoted syntax didn't parse (contributing zero AI-focused candidates), and one repository's automation dominated the top 5 results, wasting the grading budget. I logged these findings in scan-notes.md for future patches and selected kubestellar/console#24439 (ARIA labels) as my issue because it matched my profile perfectly and the repo explicitly welcomes AI-assisted development.

**Unit 6: Built voice-guard with upstream-specific rules for kubestellar; tested claim comment through the guard**

I built the voice-guard skill by writing a voice-guide.md covering my identity as a first-time contributor, five writing rules (promise next step not outcome; paste output not summaries; name the edge of what you tested; write issue-specific comments; credit classmates), a "things I never post" list, and an Upstream section with specific rules for claims and pings. I ran the guard on my claim draft via the check_voice.py script. The guard stopped twice with "the claude CLI is not on PATH" — the check_voice.py hook calls the claude binary, which wasn't in the shell PATH during those runs. I worked around this by running the guard from a terminal where the PATH was correct. Nothing in the voice-guide itself changed; the guide held up on review.

**Unit 7: Set up ship-gate concept and manifest for the ARIA-labels branch; issue closed before PR was opened**

I drafted the ship-gate manifest (ship-manifest.md) with the pre-push checks I would run before opening a PR: linting the changed TSX files, confirming the ARIA label additions didn't break existing tests, and verifying no new test failures. While preparing to push my branch, I discovered the kubestellar hive bot had already closed issue #24439 on Oct 19 — it verified every flagged element against `main` and found all ARIA labels were already present (the Auto-QA scanner was producing false positives). Because no code change was needed, no PR was opened and the ship-gate ran on a hypothetical branch rather than a real one. What changed: I would update the ship-gate setup process to check issue status before building out a full manifest, to avoid investing manifest work on an issue that closes before the PR stage.

**Unit 8: Installed pr-monitor from unit 8 materials; wrote playbook based on kubestellar's merge history; no PR to run it on**

I cloned the unit-8-starter materials and installed the pr-monitor to `~/.claude/pr-monitor/`. I wrote my playbook.md with four sections: Response rules (open with the specific change requested, never with generic thanks), Staleness thresholds (14 days based on sampling 5 merged PRs in kubestellar/console's Closed tab — most waited 7–12 days for a first review), Nudge etiquette (ping the reviewer by name, reference the PR number, attach one new piece of evidence), and Walk-away criteria (close if no response in 30 days or if maintainer explicitly asks to withdraw). Because no PR was opened, I ran `monitor.py --state` and confirmed it correctly reported NO PR on the branch. I did not run the full monitor call since there was no live PR to draft a move for. What fell short: the playbook sections were written without a live PR to calibrate against, so the staleness thresholds are estimates from historical PRs rather than confirmed against my own thread.

---

## Why I Chose This Issue

I ran my issue-scout on kubestellar/console and two other repositories in late September 2026. The scout surfaced kubestellar/console#24439 — "[Auto-QA] Interactive elements missing ARIA labels" — as a strong match against my profile. The issue was tagged `help wanted`, `ai-fix-requested`, and `triage/accepted`, and the repo's label scheme explicitly invites AI-assisted contributions. My profile is TypeScript and React, and adding ARIA labels to dashboard buttons and images is a well-scoped accessibility fix: a named set of files, a clear definition of done (every flagged element gets a label), and no product decisions open. The scout ranked it first among three candidates, all of which scored `accept`, but #24439 was the clearest fit for the skills I wanted to demonstrate — reading a live component tree and applying accessibility patterns.

My claim comment went up on October 19, 2026 (the same day the issue was filed), naming my first step (trace the INVENTORY.md and component files to verify which elements still needed labels) and committing to report back by October 27. The claim link: https://github.com/kubestellar/console/issues/24439#issuecomment-6258290635

The scout's main limitation on this search was the per-repo cap: when one repository's automation dominates the top results, it crowds out candidates from other active repos. I logged this in scan-notes.md and would address it in a future patch by adding a per-repo cap to the ranking filter, so no single repo takes more than two of the top five slots.

**Toolkit**

- Tool: issue-scout (four search queries, ranking rubric with eight checks: maintainer-alive, repo-not-archived, recent-push, not-claimed, scope-bounded, ai-policy, good-first-issue, maintainer-responsive)
- What it produced: verdict `accept` for all three candidates; #24439 ranked first on fit-to-profile criteria; full check output logged in scan-output.md
- Difficulties: Query 3's quoted syntax produced zero results; one repo's automation dominated top-5 slots and consumed grading budget
- What changed: logged per-repo cap patch in scan-notes.md; query 3 syntax corrected for future runs

---

## Understanding the Issue

**Problem Description**

The Auto-QA accessibility workflow for kubestellar/console flagged that buttons and images in several dashboard components lacked ARIA labels for screen reader users. Specifically: buttons in `CardFactoryCode.tsx`, `MissionSuggestions.tsx`, `MissionActionPanel.tsx`, `AddCardModal.tsx`, `FieldSuggestChips.tsx`, `PostConnectBanner.tsx`, and `AddCardBrowsePanel.tsx` appeared as bare `<button>` elements without `aria-label` attributes; icon-only buttons in `PodDrillDown.tsx`, `DrasiConfigModals.tsx`, and `DrasiConnectionsModal.tsx` were flagged as lacking labels; and ten `<img>` elements across mission and feedback components appeared without `alt` text.

**Expected Behavior**

Every interactive element rendered to screen-reader users should carry either visible text content that serves as its accessible name, or an explicit `aria-label` / `title` attribute. Every `<img>` element should have meaningful `alt` text.

**Current Behavior**

According to the Auto-QA scan at commit `cb2bc75`, the elements above appeared to lack ARIA labels. The scanner used regex-level detection, which cannot see text children or existing attributes — this matters because the actual fix determination required reading the rendered component, not just the raw tag.

**Affected Components**

`src/components/dashboard/` (CardFactoryCode, MissionSuggestions, MissionActionPanel, AddCardModal, FieldSuggestChips, PostConnectBanner, AddCardBrowsePanel), `src/components/drilldown/views/PodDrillDown.tsx`, `src/components/cards/drasi/` (DrasiConfigModals, DrasiConnectionsModal), and `src/components/missions/` and `src/components/feedback/` for the image elements.

**Toolkit**

- Tool: repro-check (used to structure the investigation: environment setup, what to verify, evidence format)
- What it produced: a checklist of which files to open and what to look for in each flagged element
- Difficulties: the issue was filed by an Auto-QA bot, so there was no human reproduction comment to reference; the scanner's output was regex-only and could not distinguish "missing label" from "label is a text child"
- What changed: noted in process that bot-filed Auto-QA issues require manual triage before assuming work is needed; would add this as a check in a future repro-check rubric update

---

## Reproduction Process

**Environment Setup**

I cloned my fork of kubestellar/console and ran `npm install` to install dependencies. The project uses TypeScript and React with a Node 18+ requirement. No special environment variables were needed to open and read the component files.

**Steps to Reproduce**

1. Open `src/components/dashboard/MissionActionPanel.tsx` and locate lines 89, 99, 109, 119 — the four `<button>` elements flagged by the scanner.
2. Read the JSX: each button renders a visible text child via a translation key (`{t('actions.snooze')}`, `{t('actions.dismiss')}`, etc.) or an existing `aria-label` attribute.
3. Cross-check `src/components/dashboard/AddCardBrowsePanel.tsx` lines 39–185 — the buttons there each wrap a text label directly.
4. Observed result: all flagged elements are already accessible. The scanner missed text children and existing `aria-label` attributes because its detection is regex-only.

**Reproduction Evidence**

- **Commit showing reproduction:** No commit — the reproduction was a read-only file audit showing the issue was a false positive. No code change was needed.
- **My findings:** The hive scanner bot posted its own verification on Oct 19, 2026 before I completed my audit: "Verified every flagged location against main — all are already accessible; no code change needed… These are Auto-QA scanner false positives (regex-level detection can't see text children / existing attributes). Closing as not planned." My own read of the component files confirmed this finding. The issue was legitimately closed.

**Toolkit**

- Tool: repro-check (structured the audit steps: open each flagged file, read the JSX, check for text children and existing ARIA attributes before assuming a label was missing)
- What it produced: a file-by-file audit confirming all flagged elements had accessible labels; conclusion matched the bot's automated verification
- Difficulties: the repro-check rubric is built for bugs with a reproducing crash or observable failure — a false-positive accessibility scan produces no such output, so the evidence format had to be adapted to a "confirmed not broken" finding
- What changed: the experience showed the rubric needs a "cannot reproduce — reason: false positive" verdict path with its own evidence shape; I would add that in a future revision

---

## Solution Approach

**Analysis**

The root cause of the issue was the Auto-QA scanner's limitation: regex detection of `<button` tags without reading their children or existing attributes. All flagged elements in the codebase were already accessible as of commit `cb2bc75`. No code change was required.

**Proposed Solution**

No code change was implemented. The correct resolution was confirming the false positive and accepting the maintainer's close decision.

**Implementation Plan**

1. Audit each flagged file to verify element accessibility (completed).
2. If any element had been genuinely missing a label: add `aria-label` with a descriptive string matching the button's visible purpose, following the existing pattern in the codebase.
3. Run existing accessibility test suite (if present) to verify no regressions.

No deviations to record — the plan never reached implementation because the audit confirmed no changes were needed.

**Toolkit**

- Tool: plan-check (used to structure the approach decision: what the evidence showed, what was in scope, what I was deliberately not touching)
- What it produced: a clear scope statement — audit only, no code change — matching the evidence from the reproduction audit
- Difficulties: plan-check is designed for a fix plan with files to edit; a "no change needed" conclusion doesn't fit the standard format
- What changed: nothing in the tool; noted that a future plan-check rubric should handle the "not-a-bug" verdict explicitly

---

## Testing Strategy

No code was changed, so no before/after test was run. The testing strategy for the hypothetical fix would have been: open each flagged component in a browser with VoiceOver or NVDA, navigate to each button, and confirm the screen reader announces a meaningful name. For images: confirm VoiceOver reads the `alt` text. As a programmatic check: run `axe-core` or a similar accessibility linting tool against the pages containing the flagged components.

**Toolkit**

- Tool: pr-precheck (reviewed what checks I would run before opening a PR, even though no PR was opened)
- What it produced: a checklist — lint the changed files, run the existing test suite, do a manual screen-reader spot-check on the modified components
- Difficulties: without a real PR branch, the ship-gate manifest was hypothetical
- What changed: nothing; the checks are ready for a future contribution where code actually changes

---

## Implementation Notes

**Oct 19, 2026**

Issued claim comment on kubestellar/console#24439 on the same day the issue was filed. Noted that the hive bot had also started automated work on the issue — the claim lapse was set for two hours. I expected to open a PR within 3–5 days.

**Oct 19, 2026**

The hive scanner bot posted its full verification within about 40 minutes of the issue being filed. It confirmed all flagged elements were already accessible and closed the issue as "not planned." My own file audit later that day confirmed the same finding. No code change was needed and no PR was opened.

**Oct 25, 2026**

Posted a follow-up claim comment (from my saved draft) after re-reading the thread. This was an error — the issue had been closed for six days by the time I posted it. The comment is at https://github.com/kubestellar/console/issues/24439#issuecomment-6390982713 and reads as though the issue is still open. I would not post this comment if I had checked the issue status first.

**Nov 2026**

Drafted ship-manifest.md for the hypothetical ARIA-labels PR — pre-push checks covering lint, test, and manual a11y spot-check. No branch was pushed because no code change was needed.

**Nov 2026**

Installed pr-monitor from unit-8-starter materials. Wrote playbook.md with four sections. Ran `monitor.py --state` — it correctly reported NO PR on the branch since none was opened.

**Toolkit**

- Tool: All four tools (issue-scout, repro-check, plan-check, pr-precheck) were used in sequence across the contribution arc
- What it produced: a full audit trail from issue selection through resolution, even though the resolution was "no change needed"
- Difficulties: none of the tools have a first-class "issue closed as false positive" path; each had to be adapted
- What changed: documented adaptation notes for each tool in its respective Toolkit block above

---

## Pull Request

**PR Link:** No PR opened — issue was closed as a false positive before a fix was required

**PR Description:** N/A

**Maintainer Feedback:**

- Oct 19, 2026: The kubestellar hive scanner bot verified all flagged elements against `main` and confirmed all are already accessible. Issue closed as "not planned" (false positives from regex-level scanner).
- Oct 19, 2026: I confirmed the finding through my own file audit. No code change needed; no reply was posted to the close comment.

**Status:** Closed

**Toolkit**

- Tool: pr-monitor (installed; playbook written; ran `--state` only)
- What it produced: `--state` confirmed NO PR on branch; no drafted-move was generated since there was no PR to analyze
- Difficulties: the monitor is designed for an open PR; without one, there is nothing for it to read or draft
- What changed: nothing in the tool; noted that a future contribution should open the PR before investing in the playbook calibration

---

## Learnings and Reflections

**Technical Skills Gained**

I learned how automated accessibility scanning works and where it falls short: regex detection against raw JSX cannot read text children, attribute values set via expressions, or translation-key strings, which makes it unreliable as a sole source of truth for accessibility issues. I also learned how to read a TypeScript/React component tree quickly enough to audit an accessibility flag — checking whether a `<button>` has visible text children versus needing an explicit `aria-label` is a different question than the scanner's output suggests. And I learned the kubestellar contribution workflow: the hive bot, the two-hour claim lapse window, and how automated triage works in that repo.

**Challenges Overcome**

The main challenge was deciding how to handle a contribution arc that ended with a closed issue and no PR. The honest answer — the issue was a false positive, the bot got there first, and no code change was needed — was the right one. The temptation was to treat the false positive as an opportunity to open a PR anyway (adding labels that weren't needed) or to pivot silently to a different issue. I chose instead to document what actually happened and note where each tool's design assumed a different outcome. That discipline of honest recording turned out to be more useful than a PR would have been, because it exposed real gaps in each tool's rubric.

The other challenge was the timing of my second claim comment. I posted it six days after the issue was closed without checking the thread. This was a process failure, not a technical one — the voice-guide explicitly says I should not post a plan comment into a thread whose maintainer has already resolved it. I would add an explicit pre-post check to my upstream workflow: always re-read the issue's current state before posting.

**What I'd Do Differently Next Time**

Before posting any comment on an issue, I would check whether the issue is still open and whether a maintainer or bot has already posted a resolution. For auto-filed issues (by bots like the hive scanner), I would check whether a matching automated resolution is likely before investing time in a manual audit. I would also open the PR sooner — my instinct was to do the full audit before claiming, but the two-hour hive claim window means that in this repo, speed matters. A faster initial triage (is this a real bug or a scanner artifact?) would have surfaced the false positive before I committed to the full workflow.

---

## Resources Used

- [kubestellar/console issue #24439 thread](https://github.com/kubestellar/console/issues/24439) — the primary source; the hive bot's comment explaining the false positive was the most useful single document
- [WAI-ARIA Authoring Practices, Button pattern](https://www.w3.org/WAI/ARIA/apg/patterns/button/) — used to confirm what constitutes a valid accessible name for a button (visible text child is sufficient; explicit `aria-label` is only needed for icon-only buttons)
- [MDN: Using the aria-label attribute](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-label) — reference for when `aria-label` is required versus redundant
- [axe-core rules](https://github.com/dequelabs/axe-core/blob/develop/doc/rule-descriptions.md) — cross-checked the scanner's findings against axe-core's label rules to confirm the false-positive conclusion
