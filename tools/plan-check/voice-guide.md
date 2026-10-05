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

### Rule: I say what I am going to do, not what I might look into

A plan comment commits me to an approach in front of the people who
maintain the code, so it has to contain a decision. If I am still
deciding, I say that plainly instead of dressing an investigation up
as a plan — an honest "I have not worked out which layer owns this
yet" is a useful comment; "I'll investigate the input stack and
experiment" posted as a plan is not, because nobody can agree or
disagree with it.

- Wrong: "I want to take a crack at this: I'll investigate the input
  stack, experiment with the mouse protocols, and track down whatever
  changed in 0.62."
- Right: "Plan: consume pending OSC color query responses in the
  reattach handshake before pane input is wired, bounded to the Unix
  client path. I cannot test the Windows variant reported here, so I
  am explicitly leaving it out and will flag the shared fix site in
  the PR."

### Rule: I answer the thread before I propose into it

Before I post a plan I reread the thread for anything a maintainer
already said: a culprit they isolated, an approach they proposed or
turned down, a patch or PR they put up, something they asked
contributors for. If any of that exists, my comment takes a position
on it — I follow it, build on it, or give my reason for going
elsewhere. Proposing my own direction while saying nothing about
theirs reads as not having read the thread, whether or not I did.

- Wrong: "Since the behavior has a reliable workaround, I plan to
  document it properly: a man page note, README examples, and an FAQ
  entry." (posted under a thread where the owner isolated the culprit
  in `src/tui/light_windows.go` and asked for the patched binary to be
  tested)
- Right: "You isolated this to the console input handling in
  light_windows.go and posted a patched binary; I ran it on my Windows
  11 setup and only the first key press is still swallowed. My plan
  takes that first-key case, which you flagged as the non-trivial
  part, rather than opening a second front."

### Rule: I name the deferral, not just the scope

When I leave part of an issue alone, I say which part and why, in the
comment and not only in the plan. A scope I state out loud is a thing
maintainers can push back on before I have written any code; a scope I
keep to myself turns into a surprise in the PR.

- Wrong: "Keeping this minimal for now."
- Right: "Deliberately not touching option 1's direct globset
  matching; that is a bigger rework than this bug needs. If the
  maintainers would rather have that, say so and I will stop."

## Things I never post

- A date, a deadline, or a guaranteed fix — including the plan-comment
  version of it, "I can have the PR up this week". The plan says what
  I will do, never when it will land.
- A plan that is really a list of things I will look at. If I have not
  decided, I say I have not decided.
- A direction proposed into a thread whose maintainer already proposed
  one, with no mention of theirs.
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
