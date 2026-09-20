# Rubric: is this a good first issue?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| maintainer-alive | "last 5 default-branch commits" under Repo facts: dates and authors | At least one commit by a non-bot author within 365 days of capture date | required |
| repo-not-archived | "archived:" field on the repo line under Repo facts | Value is "no" | required |
| recent-push | "last push to any branch" under Repo facts | Last push within 365 days of capture date | required |
| not-claimed | "assignees:" and "linked PRs:" under Repo facts; Comments section for phrases like "I'll take this", "working on this", "can I work on this" | No assignee AND no open linked PRs AND no claim comment acknowledged by a maintainer within the last 90 days | required |
| scope-bounded | Issue body and comment thread | Fail if: (1) the issue is explicitly a tracking list or umbrella with sub-items meant to be split into separate PRs, (2) the thread shows an unresolved design debate with no maintainer settling it, (3) it is a pure support/usage question, (4) there are 2 or more closed unmerged PRs indicating repeated abandoned attempts with no maintainer progress signal, or (5) it is a vague feature wish with no spec and a product decision still open, or (6) the issue was opened by a bot (username ending in [bot]). Pass if: the issue describes one bounded change even if the body is terse, even if it touches multiple files, and even if it lists implementation suggestions — a detailed spec or a maintainer/collaborator filing it with a good-first-issue label is sufficient evidence of bounded scope. | required |
| ai-policy | "contribution policy" line under Repo facts | No outright ban on AI-generated or AI-assisted contributions; silence passes; conditions such as disclosure, personal review, or testing pass | required |
| good-first-issue | Labels listed in the issue header; author_association of issue opener | Issue carries a "good first issue" or equivalent label, OR was opened by a maintainer/owner/member/collaborator | preferred |
| maintainer-responsive | "maintainer first-response sample" under Repo facts; Comments section author_association badges | At least 2 of 5 sampled issues have a maintainer response, OR this issue's thread contains a comment from an owner/member/collaborator | preferred |

## Verdict rule

Accept if every required check passes. Preferred checks never change the verdict; they rank accepted issues against each other. Unclear on any required check counts as fail.
