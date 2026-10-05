# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

prasanna-4

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68#issuecomment-5987845164

Following up on [my claim above](https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68#issuecomment-5852714118),
where I said I'd come back with my repro details. Here they are, and then my
plan. I'm a first-time contributor to this repo, and this is my own run in my
own environment (Python 3.13.2, Windows 11, `rank-bm25` 0.2.2) — @yulijasso
and several others have posted their own reproductions and plans above, which
I've read; I'm not writing over any of them.

**Reproduction.** The `ZeroDivisionError` is raised inside `index()`, not
inside `search()`:

```
$ python -c "
from rag.retriever.keyword_search import KeywordSearcher
s = KeywordSearcher()
s.index([])
print(s.search('python', top_k=10))
"
Traceback (most recent call last):
  File "<string>", line 4, in <module>
    s.index([])
    ~~~~~~~^^^^
  File "rag\retriever\keyword_search.py", line 25, in index
    self.bm25 = BM25Okapi(tokenized_corpus)
                ~~~~~~~~~^^^^^^^^^^^^^^^^^^
  File ".venv\Lib\site-packages\rank_bm25.py", line 52, in _initialize
    self.avgdl = num_doc / self.corpus_size
                 ~~~~~~~~^~~~~~~~~~~~~~~~~~
ZeroDivisionError: division by zero
```

**Control 1 — `search()` with no `index()` call at all:**

```
$ python -c "
from rag.retriever.keyword_search import KeywordSearcher
print(KeywordSearcher().search('python', top_k=10))
"
2026-10-04 23:31:10 [warning  ] keyword_search_empty_index
[]
```

**Control 2 — `index()` with one chunk:**

```
$ python -c "
from rag.retriever.keyword_search import KeywordSearcher
s = KeywordSearcher()
s.index([{'id':1,'text':'python content'}])
print(len(s.search('python', top_k=10)), 'result(s)')
"
2026-10-04 23:31:10 [info     ] keyword_index_built            chunk_count=1
2026-10-04 23:31:10 [info     ] keyword_search_complete        query_len=1 results_count=1
1 result(s)
```

Control 1 is the one I'd flag. It shows the guard at `keyword_search.py:38-40`
already works and `search()` returns `[]` cleanly — so `search()` is not the
broken function, and a fix there would leave the crash exactly where it is.
Control 2 shows indexing is fine for a non-empty corpus. The empty list handed
to `index()` is the single variable that flips the outcome.

**Plan.** Guard the empty-corpus case in `index()` before `BM25Okapi` is
constructed: keep the empty chunk list, set `self.bm25` back to `None`, log
the empty index, return. That is the exact state `search()` is already written
to expect, so no second code path appears. Setting `bm25` rather than just
leaving it matters for the re-index case — on a searcher that already indexed
something, a live index from the previous corpus would otherwise survive, and
only the emptied `chunks` would be keeping `search()` honest. Then remove the
strict xfail on `test_empty_index` — it's `strict=True`, so fixing the code
and leaving the marker turns the test red as an XPASS — and add a test that
`index([])` on its own doesn't raise, covering the re-index case too.

Files: `rag/retriever/keyword_search.py` and
`tests/unit/test_keyword_search.py`.

**Test plan, and where it stands.** I've since built this on
`fix/68-empty-index-guard` in my fork, so I can give the after-numbers rather
than only predictions:

- The repro command above now prints `[]` and exits 0 instead of the
  traceback.
- `pytest tests/unit/test_keyword_search.py -k empty_index` goes from
  `1 xfailed` to `1 passed` — not `1 xpassed`, which is what fixing the code
  without removing the strict marker would produce.
- Both controls unchanged: control 1 still returns `[]`, control 2 still
  returns 1 result.
- The file as a whole: `16 passed, 1 xfailed` → `18 passed`.
- Full unit suite: `377 passed, 52 xfailed` (the other seeded bugs), no
  failures. `ruff check` and `ruff format --check` are clean on both files.

I'm posting the plan rather than going straight to a PR because the two
questions at the end are worth settling first.

**Deliberately out of scope**, so it can be pushed back on before I write
anything: `search()`'s guard (it's correct — control 1), the tokenizer and
scoring path (nothing in the evidence touches them), and the other seeded
issues. I'm also not proposing a change to `rank_bm25` itself — the division
is upstream, and I think the local guard is right for this issue, but that's a
judgment call and I'd rather hear it than assume it.

**Two open questions.** First: if anything upstream in the retriever pipeline
calls `index([])`, returning an empty result silently could hide a real "no
chunks were produced" failure that the crash was surfacing loudly. I haven't
traced the callers, so that's an unknown rather than something I've ruled out
— it's why I'm logging at warning level and not info. If it should stay loud,
say so and I'll change the approach.

Second, a smaller one, and a place where the build already departed from what
I first wrote down: I'm logging the empty index under a new key,
`keyword_index_empty`, rather than reusing `keyword_search_empty_index` from
`search()`. It pairs with `keyword_index_built` in the same method, and it
keeps "the index was never built" distinguishable from "a search ran against
an empty index" — both fire on the empty path, and with one shared key you
couldn't tell them apart in a log search. Easy to change if you'd rather have
one key.

I'll report back with the result either way.

---

## Your branch

**Branch**

fix/68-empty-index-guard

**Evidence**

My Unit 2 reproduction steps, re-run against the built change. Environment:
Python 3.13.2, Windows 11, `rank-bm25` 0.2.2, the repo's `.venv`.

### Before (on `main`, commit `f89c06f`)

Repro command:

```
$ python -c "
from rag.retriever.keyword_search import KeywordSearcher
s = KeywordSearcher()
s.index([])
print(s.search('python', top_k=10))
"
Traceback (most recent call last):
  File "<string>", line 4, in <module>
    s.index([])
    ~~~~~~~^^^^
  File "C:\Users\rlpra\pathreview-ai301-fa26-s1\rag\retriever\keyword_search.py", line 25, in index
    self.bm25 = BM25Okapi(tokenized_corpus)
                ~~~~~~~~~^^^^^^^^^^^^^^^^^^
  File "C:\Users\rlpra\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\rank_bm25.py", line 83, in __init__
    super().__init__(corpus, tokenizer)
    ~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^
  File "C:\Users\rlpra\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\rank_bm25.py", line 27, in __init__
    nd = self._initialize(corpus)
  File "C:\Users\rlpra\pathreview-ai301-fa26-s1\.venv\Lib\site-packages\rank_bm25.py", line 52, in _initialize
    self.avgdl = num_doc / self.corpus_size
                 ~~~~~~~~^~~~~~~~~~~~~~~~~~
ZeroDivisionError: division by zero
```

The xfail test:

```
$ python -m pytest tests/unit/test_keyword_search.py -k "empty_index" -rA
collected 17 items / 16 deselected / 1 selected

tests\unit\test_keyword_search.py x                                      [100%]

=========================== short test summary info ===========================
XFAIL tests/unit/test_keyword_search.py::TestKeywordSearcher::test_empty_index - issue #68 (manifest H-01): BM25 keyword search raises ZeroDivisionError on an empty index
====================== 16 deselected, 1 xfailed in 0.48s ======================
```

Control 1 — `search()` with no `index()` call:

```
$ python -c "
from rag.retriever.keyword_search import KeywordSearcher
print(KeywordSearcher().search('python', top_k=10))
"
2026-10-04 23:31:10 [warning  ] keyword_search_empty_index
[]
```

Control 2 — `index()` with one chunk:

```
$ python -c "
from rag.retriever.keyword_search import KeywordSearcher
s = KeywordSearcher()
s.index([{'id':1,'text':'python content'}])
print(len(s.search('python', top_k=10)), 'result(s)')
"
2026-10-04 23:31:10 [info     ] keyword_index_built            chunk_count=1
2026-10-04 23:31:10 [info     ] keyword_search_complete        query_len=1 results_count=1
1 result(s)
```

### After (on `fix/68-empty-index-guard`, commit `ffb6d62`)

Same repro command, now exiting 0:

```
$ python -c "
from rag.retriever.keyword_search import KeywordSearcher
s = KeywordSearcher()
s.index([])
print(s.search('python', top_k=10))
"
2026-10-04 23:43:34 [warning  ] keyword_index_empty
2026-10-04 23:43:34 [warning  ] keyword_search_empty_index
[]
exit: 0
```

The same test, with the strict marker removed — `1 passed`, not `1 xpassed`:

```
$ python -m pytest tests/unit/test_keyword_search.py -k empty_index -rA
collected 18 items / 17 deselected / 1 selected

tests\unit\test_keyword_search.py .                                      [100%]

=================================== PASSES ====================================
____________________ TestKeywordSearcher.test_empty_index _____________________
---------------------------- Captured stdout call -----------------------------
2026-10-04 23:43:43 [warning  ] keyword_index_empty
2026-10-04 23:43:43 [warning  ] keyword_search_empty_index
=========================== short test summary info ===========================
PASSED tests/unit/test_keyword_search.py::TestKeywordSearcher::test_empty_index
====================== 1 passed, 17 deselected in 0.26s =======================
```

Control 1 — unchanged:

```
$ python -c "
from rag.retriever.keyword_search import KeywordSearcher
print(KeywordSearcher().search('python', top_k=10))
"
2026-10-04 23:44:00 [warning  ] keyword_search_empty_index
[]
```

Control 2 — unchanged:

```
$ python -c "
from rag.retriever.keyword_search import KeywordSearcher
s = KeywordSearcher()
s.index([{'id':1,'text':'python content'}])
print(len(s.search('python', top_k=10)), 'result(s)')
"
2026-10-04 23:44:00 [info     ] keyword_index_built            chunk_count=1
2026-10-04 23:44:00 [info     ] keyword_search_complete        query_len=1 results_count=1
1 result(s)
```

The whole file, `16 passed, 1 xfailed` before → `18 passed` after:

```
$ python -m pytest tests/unit/test_keyword_search.py
collected 18 items

tests\unit\test_keyword_search.py ..................                     [100%]

============================= 18 passed in 0.27s ==============================
```

Full unit suite, no collateral damage:

```
$ python -m pytest tests/unit -q
377 passed, 52 xfailed, 2 warnings in 10.42s
```

(The 52 remaining xfails are the other seeded bugs in the manifest, #69-#72
and the rest; none of them is mine to remove.)

Lint and format on the two changed files:

```
$ python -m ruff check rag/retriever/keyword_search.py tests/unit/test_keyword_search.py
All checks passed!
$ python -m ruff format --check rag/retriever/keyword_search.py tests/unit/test_keyword_search.py
2 files already formatted
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Run 1 — partial smoke, one package from each of the five categories
(`--only pkg-02,pkg-06,pkg-01,pkg-17,pkg-04,pkg-20`): `agreement: 6/6 scored
items`, with `categories: clear-accept 1/1  scope-creep 1/1
thread-convention 2/2  unbuildable 1/1  wrong-cause 1/1`. I spent about $1.20
here deliberately before a $4 full run: the two `thread-convention` packages
are the whole category and they fail for two different reasons (pkg-04 on the
thread, pkg-20 on the AI policy), so if my two comms checks were wrong I
wanted to find out cheaply. The harness printed the partial-run warning, as it
does on any `--only` run.

Run 2 — full, 20 scored packages, with `--save-run eval-run.txt`:
`agreement: 20/20 scored items  (bar: 18/20: PASS)`, and
`categories: clear-accept 7/7  scope-creep 4/4  thread-convention 2/2
unbuildable 3/3  wrong-cause 4/4`. This is the run in the committed
`eval-run.txt`.

Run 3 — partial, `--only pkg-04` with `--out`, after the full run: `agreement:
1/1 scored items`. This was not a revise step and changed nothing. I re-graded
one package I had already agreed on purely to read its per-check output,
because the full run's `note` column is blank on agreement and I needed the
check-level detail for the Package analysis field below. No file was edited
between run 2 and run 3, so `eval-run.txt` is still the record of run 2, and
the harness refuses to overwrite it from a partial run anyway.

Last score: 20/20, matching the `agreement:` line in the committed
`eval-run.txt`.

**Package analysis**

`pkg-04` (junegunn/fzf#4260), one of the two `thread-convention` packages. My
rubric decided `reject`; the gold label is `reject`; they agree.

It is worth naming because of how narrowly it rejected, and because the plan
itself is not bad. Four of my five other required checks passed it, and they
passed on the merits:

- `cause-grounded` — **pass**: "Diagnosis matches 'Actual: ... fzf's console
  input loop keeps reading the keys on Windows' and both controls (Ubuntu
  works; /dev/tty redirection works on Windows)."
- `scope-bounded` — **pass**: "All proposed changes are doc edits for this
  issue; plan states 'Not in scope: any change to fzf's input handling
  code.'"
- `approach-chosen` — **pass**: "Names man page execute section, README
  execute examples, and wiki FAQ with stated edits for each."
- `test-plan-observable` — **pass**: "Test plan: re-run documented command
  form and confirm j/k/q work in less, plus confirm docs build renders the new
  section."

So this is a correctly diagnosed, tightly bounded, executable, testable plan.
Every check that reads the *plan* passes it. The single required failure is
the one that reads the *comment*:

- `comment-engages-thread` — **fail**: "junegunn (OWNER) named the culprit in
  src/tui/light_windows.go and posted a patched binary (commit 8916cbc); the
  comment proposes a documentation-only plan and never mentions either."

My rubric read it that way because the check is `required` and my verdict rule
is "Any single `required` check graded `fail` makes the verdict `reject`" —
there is no weighing of one failure against four passes. That is the design.
The owner had already isolated the bug to specific lines, posted a patched
binary, and asked the reporter to test it; walking into that thread with
"here's a docs plan" is not a scoping decision, it is a comment written as if
the thread were empty. A rubric that only grades the plan half of a plan
package cannot see that at all, and `thread-convention` is the category built
to catch exactly that blindness.

The other half of the same design is the one that keeps the check honest.
`pkg-02` (sharkdp/bat#3844) has `## Thread highlights (0 comments total)` and
`pkg-14` (zellij) has a thread of nothing but other reporters confirming the
symptom — both are gold accepts, and both would have been rejected by a
cruder version of this check that demanded thread engagement unconditionally.
The pass-by-default clause is what lets one check reach a reject on pkg-04 and
a pass on those two.

**Check rationale**

The `comment-engages-thread` row of `tools/plan-check/rubric.md`, exactly as
it reads now:

> | `comment-engages-thread` | The candidate plan comment read against the `## Thread highlights` section (live: the issue's comment thread), with attention to comments from an OWNER, MEMBER, COLLABORATOR, or CONTRIBUTOR. | Pass by default when the thread carries no direction for a contributor: no comments, or only other users confirming the symptom. When someone with a repo role has named a culprit, proposed or rejected an approach, posted a patch or an open PR, or asked contributors for something, pass if the comment follows that direction, builds on it, engages the named PR, or states a reason for taking a different route. Fail if the comment proposes a direction the thread already rejected, or proposes its own while leaving an owner's isolated culprit, posted patch, or direct request unmentioned. The plan body engaging the thread does not rescue a comment that does not: this check reads the comment, because the comment is what the thread sees. | required |

Four decisions in it are deliberate, and each exists because the shorter
version breaks a specific package.

First, **the role filter** ("with attention to comments from an OWNER, MEMBER,
COLLABORATOR, or CONTRIBUTOR"). Without it the check cannot tell direction
from noise. `pkg-14`'s thread has four comments, all `NONE`, all other users
saying "same on my machine" — there is nothing there to engage, and a check
that counted comments rather than roles would have failed a gold accept.
`pkg-04`'s decisive comment is tagged OWNER. The role tag is the whole
signal.

Second, **pass by default**. I wrote the fail conditions as the narrow case
and made silence pass, rather than requiring every comment to demonstrate
thread-awareness. `pkg-02` has a zero-comment thread; seven of the twenty
packages are gold accepts and most of their threads ask nothing of them. A
check that started from suspicion would have leaked failures across the whole
`clear-accept` category.

Third, **"or states a reason for taking a different route."** I rejected the
stricter rule — the comment must follow the maintainer's direction — because
it would make disagreeing with a maintainer automatically disqualifying, which
is wrong on the merits and would have mis-rejected `pkg-09`, where the
contributor picks one of three options a collaborator listed and says why.
Following, building on, engaging, or reasoned departure all pass. Only silence
about an existing direction fails.

Fourth, and the one I had to add explicitly: **"The plan body engaging the
thread does not rescue a comment that does not."** Without that sentence the
check is ambiguous about which half of the package it reads, and a grader can
satisfy it from the plan's scope section while the posted comment stays
oblivious. The comment is the artifact the thread actually receives, so the
check names it and says so twice.

**Trade-offs**

What this check gives up is the thread it cannot read. It grades the
`## Thread highlights` block, which is a curated excerpt — in live mode, the
whole thread. On a long thread where direction is buried in comment 40 of 120,
or where a maintainer's steer lives in a linked PR review rather than the
issue, my check will pass a comment that a maintainer would consider
oblivious. I accept that miss, because tightening it means demanding
engagement by default, and that is the error that costs real packages:
`pkg-02` and `pkg-14` are gold accepts whose threads contain nothing to
engage.

The sharper trade-off is one I made on purpose and can point at a package for.
I weighted `unknowns-marked` as `preferred` rather than `required`, and
`pkg-04` is where that shows: the same run graded it

> `unknowns-marked` — **fail**: "Plan states its diagnosis and doc approach
> with no risk/unknown/open-question line."

A second required failure, on a package that was already rejected — so it cost
nothing there. But had I made that check `required`, it would be live on every
package, and the seven gold accepts do not all state risks in the same shape:
`pkg-03` has no `Risk:` line at all and instead carries its uncertainty inline
("noting any tool that does not support `--` as an open question in the PR
description"), while `pkg-05` writes "Risk: none identified beyond one extra
network request". A required honesty check would have to pass both of those
and still fail `pkg-04`, and I did not trust myself to draw that line without
flipping an accept. Reported, never counted, was the safer call — and the
failure families the eval set is actually built around are all covered by the
six required checks.

Nothing else changed, and here is how I know. There was no revise loop: the
six-package smoke run agreed 6/6, the full run agreed 20/20 on the first
attempt, and no check was ever loosened after being measured — so no canary
was needed, and the `thread-convention` category never had the chance to flip.
The one post-hoc run (`--only pkg-04`) edited nothing and re-confirmed the same
`reject`. The `categories:` line in the committed `eval-run.txt` records
`thread-convention 2/2` on that single full run.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
