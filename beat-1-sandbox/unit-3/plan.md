# Plan: issue #68 — KeywordSearcher raises ZeroDivisionError on an empty index

Issue: codepath/pathreview-ai301-fa26-s1#68 (manifest H-01)
Repo: codepath/pathreview-ai301-fa26-s1 (fork: prasanna-4/pathreview-ai301-fa26-s1)

## Diagnosis

`KeywordSearcher.index()` builds a `BM25Okapi` from the tokenized corpus
unconditionally. `rank_bm25` divides by the corpus size while computing the
average document length, so an empty chunk list makes that division a
division by zero and `index()` raises before it ever returns.

The failure is in `index()`, not in `search()`. `search()` already has the
guard this bug is usually blamed on — `rag/retriever/keyword_search.py:38-40`:

```python
if not self.bm25 or not self.chunks:
    logger.warning("keyword_search_empty_index")
    return []
```

That guard is correct and is never reached, because the exception is thrown
one call earlier. Any fix that only hardens `search()` would leave the bug
exactly where it is.

### Repro evidence I am relying on

From my Unit 2 reproduction of this issue, re-run against the current
checkout of `main` (Python 3.13.2, Windows 11, `.venv` from the repo's
`pyproject.toml`):

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
  File "...\rag\retriever\keyword_search.py", line 25, in index
    self.bm25 = BM25Okapi(tokenized_corpus)
                ~~~~~~~~~^^^^^^^^^^^^^^^^^^
  File "...\.venv\Lib\site-packages\rank_bm25.py", line 52, in _initialize
    self.avgdl = num_doc / self.corpus_size
                 ~~~~~~~~^~~~~~~~~~~~~~~~~~
ZeroDivisionError: division by zero
```

Two control runs isolate the trigger:

```
$ # Control 1: search() without index() at all
$ python -c "
from rag.retriever.keyword_search import KeywordSearcher
print(KeywordSearcher().search('python', top_k=10))
"
2026-10-04 23:31:10 [warning  ] keyword_search_empty_index
[]

$ # Control 2: index() with one chunk
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

Control 1 shows `search()`'s empty-index path works and returns `[]`, so
`search()` is not the broken function. Control 2 shows `index()` is fine for
a non-empty corpus. The one variable that flips the outcome is the empty
list handed to `index()`.

The repo's own test suite already pins this. `tests/unit/test_keyword_search.py:134-143`
carries a strict xfail naming this issue:

```
$ python -m pytest tests/unit/test_keyword_search.py -k empty_index -rA
XFAIL tests/unit/test_keyword_search.py::TestKeywordSearcher::test_empty_index
  - issue #68 (manifest H-01): BM25 keyword search raises ZeroDivisionError on an empty index
16 deselected, 1 xfailed in 0.48s
```

## Scope

**In scope.** One bounded change: make `index()` tolerate an empty chunk
list, so that indexing nothing leaves the searcher in the same state it has
before `index()` is called, and `search()`'s existing guard returns `[]`.
Plus removing the strict xfail marker on `test_empty_index`, which is the
repo's stated workflow for a seeded bug and which `strict=True` requires —
with the bug fixed, leaving the marker in place turns the test red as
XPASS.

**Not in scope.** I am deliberately not touching:

- `search()` or its guard. It is already correct; control 1 shows it
  working. Adding a second guard there would be belt-and-braces around a
  function that is not failing.
- `_tokenize()` and the BM25 scoring path. Nothing in the evidence
  implicates them.
- The other seeded issues in the manifest (#69-#72). Each has its own
  xfail and its own issue; folding any of them in would make this a
  different change.
- Upgrading or patching `rank_bm25`. The empty-corpus division is upstream
  behavior, and guarding at our call site is the smaller, local fix. If
  maintainers would rather push this upstream, that is their call to make,
  and I would rather ask than assume.

## Files I will touch

- `rag/retriever/keyword_search.py` — the guard in `index()`.
- `tests/unit/test_keyword_search.py` — remove the `@pytest.mark.xfail`
  block on `test_empty_index` (lines 134-137); add one test that indexing
  an empty list does not raise and leaves the searcher searchable.

## Approach

1. In `index()`, handle the empty-corpus case before constructing
   `BM25Okapi`: store the (empty) chunk list, set `self.bm25` back to
   `None`, log the empty index at warning level in the same `structlog`
   style the module already uses, and return. `search()`'s existing
   `if not self.bm25 or not self.chunks` guard then returns `[]` without a
   second code path being added.

   Setting `self.bm25 = None` explicitly, rather than just leaving it
   alone, matters for the re-index case: on a searcher that has already
   indexed something, `self.bm25` holds a live index from the previous
   corpus, and only the emptied `self.chunks` would be keeping `search()`
   honest. Clearing both makes the post-condition the same whether the
   searcher is fresh or being re-indexed, instead of depending on which
   half of the `or` fires.
2. Remove the strict xfail marker from `test_empty_index` so the existing
   assertion (`results == []`) is enforced rather than expected to fail.
3. Add a test that calls `index([])` directly and asserts it does not
   raise, so the regression is pinned at the site that actually broke
   rather than only through `search()`. The same test covers the
   re-index case: index a real chunk, then `index([])`, and assert the
   subsequent search returns `[]` rather than hits from the previous
   corpus.
4. Run the full `test_keyword_search.py` file to confirm the other 16 tests
   are unaffected.

I am choosing the guard in `index()` over re-indexing with a sentinel
document, or over catching `ZeroDivisionError` around the constructor,
because the state it produces — `bm25 is None`, `chunks == []` — is the
state `search()` is already written to expect, which is what makes this a
three-line change instead of a new code path.

## Test plan

My Unit 2 repro steps, re-run against the built change, with what I expect
to see after the fix:

1. **The repro command.** The script above currently ends in a
   `ZeroDivisionError` traceback. After the fix it must print `[]` and exit
   0, with the `keyword_search_empty_index` warning logged.
2. **The xfail test, un-marked.** `python -m pytest
   tests/unit/test_keyword_search.py -k empty_index -rA` currently reports
   `1 xfailed`. After the fix and the marker removal it must report
   `1 passed` — not `1 xpassed`, which is what I would get if I fixed the
   code and left the marker, and which `strict=True` turns into a failure.
3. **The new direct test.** `index([])` on its own raises no exception,
   and re-indexing an empty list over a populated searcher leaves
   `search()` returning `[]` rather than stale hits.
4. **Control 1 unchanged.** `search()` without `index()` still returns `[]`
   and still logs `keyword_search_empty_index`.
5. **Control 2 unchanged.** `index()` with one chunk still builds the index
   and still returns 1 result for `"python"`.
6. **No collateral damage.** The full `tests/unit/test_keyword_search.py`
   file goes from `16 passed, 1 xfailed` to `18 passed` (17 existing plus
   the new one), with no failures and no xpasses.

Steps 1, 2, 4 and 5 are the before/after pairs: each is something I have
already run and recorded output for, so the change is witnessed by a
difference and not by a suite that was green beforehand.

## Risks and unknowns

- **The empty index may be masking a caller bug.** If something upstream in
  `ingestion/` or the retriever pipeline calls `index([])`, a silent `[]`
  could hide a real "no chunks were produced" failure that the crash was
  loudly surfacing. I have not traced the callers, so this is an unknown,
  not a reassurance. I am logging at warning level rather than info so the
  condition stays visible, and I will say so in the PR.
- **Whether maintainers want this guarded upstream instead.** The division
  by zero is `rank_bm25`'s, not ours. I think the local guard is right for
  this issue, but that is a judgment, not something the evidence settles.
- **Log level and message wording.** I am matching the existing
  `keyword_search_empty_index` key from `search()` so the two paths are
  greppable together. If the repo would rather have distinct keys for
  "indexed nothing" and "searched an empty index", that is a trivial
  change and I would rather be told than guess.
- **I have not run the full repository test suite**, only this file. The
  change is local to one method, but I will run the whole suite before
  opening the PR and report the result there.

## Deviations

One thing changed between the plan and the build, and it is the one the
plan had already flagged as open.

**The log key.** The plan said I would reuse the existing
`keyword_search_empty_index` key "so the two paths are greppable
together", and listed the alternative — distinct keys for "indexed
nothing" and "searched an empty index" — as a thing I would rather be
told than guess at. Building it, I used a distinct key,
`keyword_index_empty`.

Why I changed it: the guard lives in `index()`, and the two log lines
in that method are already `keyword_index_built` and now
`keyword_index_empty`, which read as a matched pair for the same
decision — did this call build an index or not. Reusing
`keyword_search_empty_index` would have put a `keyword_search_*` key
inside `index()`, which misattributes the event to the wrong method.
The after-run of the repro command shows why this is the better of the
two: both lines fire, in order, and they are distinguishable.

```
2026-10-04 23:43:34 [warning  ] keyword_index_empty
2026-10-04 23:43:34 [warning  ] keyword_search_empty_index
[]
```

Had I kept the plan's wording, that output would have been the same key
twice, and "the index was never built" would have been indistinguishable
from "a search ran against an empty index" in any log search. I have
flagged the choice in the comment as still open to the maintainers'
preference, because it is a naming call and not an evidence call.

**Nothing else moved.** The guard went in at the site the plan named, in
`index()` before `BM25Okapi` is constructed; the strict xfail came off
`test_empty_index`; the new regression test covers both the direct
`index([])` call and the re-index case. The six test-plan items all ran
with the results the plan predicted, including `1 passed` rather than
`1 xpassed` on `-k empty_index`.

Worth recording that the re-index clause — setting `self.bm25 = None`
explicitly rather than leaving it alone — is not a deviation but a
pre-build plan revision. Running `plan-check` on the drafts before
building surfaced that "leave `self.bm25` as `None`" is only true on a
fresh searcher, and that on a re-indexed one a stale index would survive
with only the emptied `chunks` keeping `search()` honest. I corrected the
plan and then built the corrected version, so the posted plan and the
diff agree on this point.
