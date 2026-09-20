# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68

**Verdict output**

All three are in the scoped repo (codepath/pathreview-ai301-fa26-s1), so all three are eligible candidates. Repo-level evidence (shared
  by all three, gathered today 2026-09-20 via the GitHub API since gh isn't installed here):

  - archived: false, pushed_at: 2026-09-16T21:48Z (4 days ago)
  - Last 5 default-branch commits all by Aburke225, a non-bot COLLABORATOR, newest 2026-09-16
  - No contribution/AI policy file exists (CONTRIBUTING.md lives at docs/CONTRIBUTING.md and says nothing about AI; no AI_POLICY.md; the
    PR template has no AI-disclosure box) → silence passes
  - Maintainer first-response sample (5 most recently updated issues: #68, #69, #73, #52, #43): 2 of 5 (#52, #43) have an Aburke225
    COLLABORATOR reply → preferred check passes, at the threshold
  - The repo has zero pull requests, open or closed, so no candidate has a linked PR or an abandoned-attempt history

  Ranked read-out — all three accept:

  1. #68 — Keyword search ZeroDivisionError on empty index. Best fit: Python, and the fix is explicitly a matter of copying a pattern
     already in the file — search() already early-returns when self.bb25/self.bm25 is unset (keyword_search.py:38-40), so index() just
     needs the mirrored guard. That is "read and follow existing code patterns" almost literally, plus the xfail removal teaches the
     repo's seeded-bug workflow. Classmate yulijasso has claimed it and posted a reproduction, but the Path Review house rule says a
     classmate's claim doesn't block — claim anyway.
  2. #69 — Output parser crashes on top-level JSON array. Also Python with a test to unmark, but the fallback needs you to decide how an
     array maps onto the review structure, so slightly more inventing than following. Classmate jacho15 claimed it on 2026-09-19; again
     non-blocking.
  3. #73 — README and .env.example disagree on the LLM API key. Cleanly bounded, smallest effort (1–2 hrs), and completely untouched —
     zero comments, no classmate interest. Ranked last only against your fit profile: it's docs/config reconciliation, so it teaches you
     least about how the project's code is structured.

  One tension worth naming: not-claimed passes on #68 and #69 purely because the house rule discounts classmate claims, and both
  classmates have posted real reproductions. The rubric accepts them as written, and that is the verdict — but #73 is the only one of the
  three where you'd be first on the ground.

  [
    {
      "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68",
      "checks": [
        {"name": "maintainer-alive", "grade": "pass", "evidence": "Last 5 default-branch commits all by non-bot Aburke225, newest
  2026-09-16 (4 days before capture)"},
        {"name": "repo-not-archived", "grade": "pass", "evidence": "Repo API reports archived: false"},
        {"name": "recent-push", "grade": "pass", "evidence": "pushed_at 2026-09-16T21:48:27Z, within 365 days"},
        {"name": "not-claimed", "grade": "pass", "evidence": "assignees: []; repo has zero PRs so no linked PRs; only claim is yulijasso
  (author_association NONE, a classmate) which the Path Review house rule discounts, and no maintainer acknowledged it"},
        {"name": "scope-bounded", "grade": "pass", "evidence": "One bounded change across rag/retriever/keyword_search.py and its test,
  filed by a COLLABORATOR with a good-first-issue label; no umbrella, debate, or support question"},
        {"name": "ai-policy", "grade": "pass", "evidence": "No AI policy anywhere: docs/CONTRIBUTING.md is silent, no AI_POLICY.md, PR
  template has no disclosure box"},
        {"name": "good-first-issue", "grade": "pass", "evidence": "Labels include 'good first issue'; opener Aburke225 is
  author_association COLLABORATOR"},
        {"name": "maintainer-responsive", "grade": "pass", "evidence": "2 of 5 sampled recently-updated issues (#52, #43) have
  COLLABORATOR replies from Aburke225; this thread has none"}
      ],
      "verdict": "accept"
    },
    {
      "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/69",
      "checks": [
        {"name": "maintainer-alive", "grade": "pass", "evidence": "Last 5 default-branch commits all by non-bot Aburke225, newest
  2026-09-16 (4 days before capture)"},
        {"name": "repo-not-archived", "grade": "pass", "evidence": "Repo API reports archived: false"},
        {"name": "recent-push", "grade": "pass", "evidence": "pushed_at 2026-09-16T21:48:27Z, within 365 days"},
        {"name": "not-claimed", "grade": "pass", "evidence": "assignees: []; repo has zero PRs so no linked PRs; the only claim ('Hi, I'd
  like to attempt this!') is from jacho15, author_association NONE, discounted by the house rule and unacknowledged by any maintainer"},
        {"name": "scope-bounded", "grade": "pass", "evidence": "Single bug in rag/generator/output_parser.py plus removing the H-02 xfail
  marker; COLLABORATOR-filed with good-first-issue label"},
        {"name": "ai-policy", "grade": "pass", "evidence": "No AI policy anywhere: docs/CONTRIBUTING.md is silent, no AI_POLICY.md, PR
  template has no disclosure box"},
        {"name": "good-first-issue", "grade": "pass", "evidence": "Labels include 'good first issue'; opener Aburke225 is
  author_association COLLABORATOR"},
        {"name": "maintainer-responsive", "grade": "pass", "evidence": "2 of 5 sampled recently-updated issues (#52, #43) have
  COLLABORATOR replies from Aburke225; this thread has none"}
      ],
      "verdict": "accept"
    },
    {
      "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/73",
      "checks": [
        {"name": "maintainer-alive", "grade": "pass", "evidence": "Last 5 default-branch commits all by non-bot Aburke225, newest
  2026-09-16 (4 days before capture)"},
        {"name": "repo-not-archived", "grade": "pass", "evidence": "Repo API reports archived: false"},
        {"name": "recent-push", "grade": "pass", "evidence": "pushed_at 2026-09-16T21:48:27Z, within 365 days"},
        {"name": "not-claimed", "grade": "pass", "evidence": "assignees: []; zero PRs in the repo; comments: 0, so no claim comment of
  any kind"},
        {"name": "scope-bounded", "grade": "pass", "evidence": "One bounded change making README.md and .env.example agree on
  OPENROUTER_API_KEY; COLLABORATOR-filed with good-first-issue label, est. 1-2 hours"},
        {"name": "ai-policy", "grade": "pass", "evidence": "No AI policy anywhere: docs/CONTRIBUTING.md is silent, no AI_POLICY.md, PR
  template has no disclosure box"},
        {"name": "good-first-issue", "grade": "pass", "evidence": "Labels include 'good first issue'; opener Aburke225 is
  author_association COLLABORATOR"},
        {"name": "maintainer-responsive", "grade": "pass", "evidence": "2 of 5 sampled recently-updated issues (#52, #43) have
  COLLABORATOR replies from Aburke225; this thread has none"}
      ],
      "verdict": "accept"
    }
  ]

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

Run 1: 15/20 — scope-bounded misfired in both directions (wrongly rejected issue-01, issue-04, issue-19; wrongly accepted issue-15, issue-20). Run 2 (partial --only on 5 issues after rubric fix): 4/5 — issue-20 still accepted. Run 3 (partial --only issue-20 after adding bot-filed signal): 1/1. Run 4 (full): 20/20 (bar: 18/20: PASS).

**Issue analysis**

issue-01 (category: clear-accept). My rubric initially decided reject; gold label is accept. The scope-bounded check failed because the issue body lists multiple pages to update (a new task page, manage-pkgs.rst, pip-interoperability.rst, new-features.md). My rubric read "multiple files" as an umbrella issue. The gold label treats it as accept because the issue has a detailed spec with named files and a single coherent goal — adding a permanent docs home for one feature. The rubric was penalizing detail rather than reading whether the work was one bounded change. I fixed the pass condition to explicitly say that touching multiple files or listing implementation suggestions does not make an issue unbounded.

**Check rationale**

The scope-bounded check currently reads: "Fail if: (1) the issue is explicitly a tracking list or umbrella with sub-items meant to be split into separate PRs, (2) the thread shows an unresolved design debate with no maintainer settling it, (3) it is a pure support/usage question, (4) there are 2 or more closed unmerged PRs indicating repeated abandoned attempts with no maintainer progress signal, or (5) it is a vague feature wish with no spec and a product decision still open, or (6) the issue was opened by a bot (username ending in [bot]). Pass if: the issue describes one bounded change even if the body is terse, even if it touches multiple files, and even if it lists implementation suggestions — a detailed spec or a maintainer/collaborator filing it with a good-first-issue label is sufficient evidence of bounded scope."

I wrote it this way because the first version used a single vague condition ("one self-contained change") that the model applied inconsistently. Listing explicit fail conditions forces the model to check each one rather than make a holistic judgment, and the explicit pass conditions prevent it from penalizing terse bodies (issue-04) or detailed specs (issue-01).

**Trade-offs**

The bot-filed condition (6) is the sharpest trade-off. It catches issue-20 (opened by cursor[bot]) but would reject a legitimate issue if a maintainer used a bot account to file it. I accept that miss because a bot-filed issue with no spec and no maintainer follow-up is a reliable reject signal in practice. I confirmed nothing else changed by re-running --only issue-20 after adding condition (6): 1/1, and the prior 19 issues were unaffected — the full run stayed at 20/20.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. Issue #68 is a Python bug in a Python/RAG codebase, which matches my experience. The fix is estimated at a few hours and the issue body names the exact file and points to an existing pattern to follow — that fits the time I have and the kind of learning I want (reading and following real project code, not inventing).

2. The skill correctly identified that the fix mirrors a guard already present in keyword_search.py, and that the xfail removal teaches the repo's seeded-bug test workflow. What the rubric could not weigh is that a classmate (yulijasso) has already posted a reproduction, which means the environment setup path is partially documented in the thread — that lowers my setup risk in Unit 2.

3. The main difficulty in claiming it is that yulijasso has already commented. The Path Review house rule says classmate claims don't block, so I can claim it, but I should move quickly and make sure my approach adds something distinct.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
