This term I built a four-tool workflow for open source contributions and ran it on a real accessibility issue in an active Kubernetes UI project. Each tool is an AI-assisted evaluation harness: I wrote the rubric and the judgment rules, the AI ran the eval and flagged where my rules broke.

The contribution itself didn't end with a merged pull request — the issue was a false positive from an automated scanner, and the project's own bot verified that before I could open a PR. What I got instead was a detailed record of what each tool got right, what it couldn't handle, and where my own process failed (I posted a claim comment six days after the issue closed without checking the status first). That record turned out to be more instructive than a straightforward merge would have been.

The main thing I changed because of a tool: my issue-scouting rubric's scope check was too vague ("one self-contained change"), so the AI applied it inconsistently across a 20-issue eval set. I rewrote it with six explicit fail conditions and an explicit pass condition. It went from 15/20 to 20/20 on the eval.

The main thing I'd do differently: check the current issue status before every comment, not just before the first one.

https://github.com/prasanna-4/ai301-coursework/blob/main/beat-3-portfolio/technical-writeup.md

#OpenSource #Accessibility #AIEngineering #CodePath
