# Contribution: [Auto-QA] Interactive elements missing ARIA labels

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** Prasanna
**GitHub Username:** prasanna-4
**Issue:** https://github.com/kubestellar/console/issues/24439
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `1` unless you make more than one contribution this term; it
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

**Unit 6: Installed voice guard and repo memory file; set up canonical voice guide with Upstream section for claims, questions, and pings**

I installed the voice guard by copying check_voice.py and hooks-settings.json to ~/.claude/voice-guard/, merging the hook into Claude settings, and creating reference files for repro-check and plan-check to read the canonical voice-guide.md. The scope patch replaced scope-bounded with scope-feasible in issue-scout/rubric.md, making scope inform difficulty rather than reject issues. The repo-memory template (CLAUDE.local.md) in console/ contains the repo's AI policy, build/test commands, and subsystem orientation. Nothing broke during setup - all commands executed cleanly and the guard fires on gh issue comment commands.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I searched for first-contribution issues using four queries in my issue-scout skill, targeting TypeScript/React projects with AI focus and the "good first issue" or "help wanted" labels. My scan surfaced kubestellar/console#24439 in the top 5, graded it against my rubric, and it passed all required checks with an ACCEPT verdict.

The scout's verdict highlighted why this issue fits my profile: it's a React + TypeScript component issue with exact file:line specifications (~30 sites), filed by a MEMBER, and the repo's CONTRIBUTING.md explicitly welcomes AI-assisted development. The difficulty note for this issue is straightforward — the issue clearly names where the change lives (the InteractiveElements table in INVENTORY.md and corresponding component files) and the change is bounded (adding ARIA labels to specific elements).

I hope to learn more about accessibility standards and how to contribute to a Kubernetes-based UI project. The scout held up well on this issue: it correctly identified the repo's AI policy, the MEMBER filing, and the bounded scope from the file:line spec. The only tension was the maintainer-responsive check which failed (0 of 6 sampled issues had maintainer comments), but this is a sample-based check and doesn't affect the accept/reject verdict.

**Claim posted:** https://github.com/kubestellar/console/issues/24439#issuecomment-6390982713

**Toolkit**

- Tool: issue-scout (search strategy and rubric) to scan GitHub for first-contribution candidates
- What it produced: The scan returned 274 unique results; after ranking and filtering, kubestellar/console#24439 was accepted as the top candidate with a straightforward difficulty note
- Difficulties: The maintainer-responsive check failed on the sample (0/6 responses), but this is a preference check that doesn't affect the verdict
- What changed: I applied the scope patch from Unit 6, replacing scope-bounded with scope-feasible in the rubric, allowing bounded issues with open edges to be accepted

---

## Understanding the Issue

**Problem Description**

Interactive elements in the KubeStellar console UI (buttons, input fields, clickable divs, and links) are missing ARIA labels and descriptions, making them inaccessible to screen reader users and failing WCAG 2.1 accessibility compliance.

**Expected Behavior**

All interactive elements should have appropriate ARIA labels (aria-label or aria-labelledby attributes) that describe their purpose to screen readers, enabling keyboard and assistive technology users to understand and interact with the UI.

**Current Behavior**

The Auto-QA workflow identified 30+ interactive element sites missing ARIA labels. These elements are not labeled for assistive technologies, breaking accessibility for users relying on screen readers.

**Affected Components**

Components in web/src/components/ directory, particularly tables, modals, and interactive UI elements. The INVENTORY.md file tracks the specific locations.

**Toolkit**

- Tool: issue-scout rubric to verify scope and acceptance
- What it produced: Confirmation of bounded scope and straightforward difficulty
- Difficulties: none met
- What changed: nothing

---

## Reproduction Process

**Environment Setup**

Operating System: Windows 11 (WSL2 with Ubuntu 22.04)
Node.js: v20.10.0
Go: 1.26.4
npm: 10.2.4

Project setup: Cloned https://github.com/kubestellar/console.git and ran ./start-dev.sh to start the dev server (backend on :8080, frontend on :5174). Setup was clean once WSL2 environment was prepared. The CONTRIBUTING.md was clear on requirements.

**Steps to Reproduce**

1. Clone the kubestellar/console repository: git clone https://github.com/kubestellar/console.git
2. Review the INVENTORY.md file to identify interactive elements listed in the Auto-QA findings
3. Open the console UI in a browser at http://localhost:5174 (after running ./start-dev.sh)
4. Use browser DevTools to inspect interactive elements (buttons, input fields, divs with click handlers)
5. Check for missing aria-label or aria-labelledby attributes on these elements

**Reproduction Evidence**

- **Commit showing reproduction:** none (reproduction is verification-only; no code changes in this report)
- **Screenshots/logs:** The issue references 30+ interactive element sites across components. The INVENTORY.md file at repo root tracks component inventory; the Auto-QA workflow validates ARIA compliance.
- **My findings:** The issue is reproducible through INVENTORY.md validation and component inspection. The Auto-QA workflow that filed this issue has identified which elements are missing labels, and those findings are accurate for the current codebase state.

**Toolkit**

- Tool: Manual inspection and CONTRIBUTING.md guidance
- What it produced: Verification that the issue describes real, reproducible accessibility gaps
- Difficulties: none met
- What changed: nothing

---

## Solution Approach

**Analysis**

The root cause is that interactive elements in the React components were built without ARIA labels. The Auto-QA workflow scans INVENTORY.md against component files to detect missing accessibility attributes. The fix requires adding aria-label or aria-labelledby attributes to the 30+ interactive elements identified in the issue.

**Proposed Solution**

Add aria-label attributes to interactive elements (buttons, inputs, clickable divs, links) as specified in the issue's file:line locations. The labels should describe the element's purpose in user-facing language.

**Implementation Plan**

1. Read the complete list of interactive elements from the issue
2. Locate each element in the component files specified
3. Add appropriate aria-label attributes to each element
4. Run the existing accessibility tests and Auto-QA workflow to verify compliance
5. Ensure all 30+ elements now have labels

Record deviations here as they happen: what changed mid-build and why.

**Toolkit**

- Tool: Component inspection and CONTRIBUTING.md
- What it produced: High-level approach to fixing the accessibility issue
- Difficulties: none met
- What changed: nothing

---

## Testing Strategy

Run the repo's existing test suite to verify accessibility compliance:

```bash
bash scripts/consistency-test.sh
bash scripts/api-contract-test.sh
cd web && npx playwright test
```

Verify that the Auto-QA workflow passes and the issue's accessibility findings are resolved. Before and after testing: inspect the same interactive elements with browser DevTools to confirm aria-label attributes are present and meaningful.

**Toolkit**

- Tool: CONTRIBUTING.md test commands
- What it produced: Clear list of tests to verify the fix
- Difficulties: none met
- What changed: nothing

---

## Implementation Notes

A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- KubeStellar CONTRIBUTING.md for development conventions and AI policy
- INVENTORY.md in kubestellar/console for tracking interactive element locations
- docs/AI-QUALITY-ASSURANCE.md for understanding CI gates and quality checks

