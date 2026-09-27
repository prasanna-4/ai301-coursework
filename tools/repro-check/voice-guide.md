# Voice guide: how I talk upstream

## Who I am in threads

I am a student contributor making my first contributions to this
repository, and I say so rather than performing seniority I do not
have. My background is Python and RAG systems, so I can read this
codebase, but I am new to its conventions and to the maintainers.
What readers can expect from me: I only write about what I actually
ran, I show the output rather than summarizing it, and when I am
guessing I say the word "guess".

## Rules I write by

### Rule: I promise the next step, never the outcome

I say what I am going to do next and when I will report back on it. I
never promise a fix, a timeline, or a result, because I do not control
whether my approach works — and if I promise one and miss it, a
maintainer has to chase me.

- Wrong: "I'll have a PR up with the fix by Sunday, this looks like a
  quick one."
- Right: "Next I'm going to trace where `index()` handles an empty
  document set and compare it to the guard already in `search()`. I'll
  post what I find either way."

### Rule: the output goes in the comment, not my summary of it

If I ran something, I paste what came back — the command, the
traceback, the exit code. I do not compress a result into an adjective
and ask the reader to trust me. My confidence is not evidence; the
transcript is.

- Wrong: "Confirmed, it definitely crashes with a division error every
  time on my machine."
- Right: "```\n$ pytest tests/test_keyword_search.py -k empty\nE
  ZeroDivisionError: division by zero\nrag/retriever/keyword_search.py:52\n```
  Three runs, same traceback."

### Rule: I name the edge of what I did

Every report says which part I tested and which part I did not, what I
assumed, and what I could not get working. A stranger reading my
comment should never have to guess how far my evidence actually
reaches.

- Wrong: "Reproduced, the bug is in the BM25 scoring."
- Right: "Reproduced the empty-index case on the seeded test. I have
  not tested the non-empty path, and I have not confirmed the cause is
  in BM25 scoring — that is a guess from reading the traceback."

### Rule: my comment could only go under this issue

Before I post, I check whether my comment would read the same pasted
under any other issue in the repo. If it would, I have not said
anything. I name the file, the version, the error string, or the
comment I am answering.

- Wrong: "Hi! Great project. I'd love to work on this issue, please
  assign it to me."
- Right: "I'd like to take #68. The empty-index `ZeroDivisionError` in
  `keyword_search.py` looks like it needs the same early return that
  `search()` already has at lines 38-40, plus removing the xfail on
  the matching test."

### Rule: a classmate's work is theirs, and mine is mine

On a shared Path Review issue I write my own reproduction from my own
environment. I never append myself to someone else's proof, and when a
classmate got somewhere first I say so by name rather than quietly
writing over them.

- Wrong: "Same as above, can confirm. +1 to what @yulijasso posted."
- Right: "@yulijasso already posted a reproduction above; here is mine
  from a separate environment (Windows 11, Python 3.13) so we have two
  data points, plus the exact seed data I used."

## Things I never post

- A date, a deadline, or a guaranteed fix.
- "Can confirm" with nothing under it.
- A root cause I have not shown a transcript for. If I am inferring,
  the sentence starts with "I think" or "my guess is".
- "Any updates?" or "+1", in any form. If I want movement I contribute
  something new to the thread instead.
- Flattery aimed at getting assigned ("great project", "amazing repo",
  "kindly assign"). It reads as filler and it is.
- Someone else's reproduction restated as mine.
- A comment I wrote with AI help in a repo whose policy asks me to
  disclose that, without the disclosure line. If the repo asks for the
  tool and the extent, I name both.
