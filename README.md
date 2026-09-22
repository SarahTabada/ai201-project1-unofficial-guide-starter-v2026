# The Unofficial Guide

Sarah Tabada. Campus_Life Corpus

# Unit 1

## What This Does

This project indexes a set of campus-advice documents (the `campus_life` corpus) and answers student questions by retrieving the most relevant document excerpts and asking a generation model to produce a grounded reply. It answers administrative and campus-life questions such as add/drop deadlines, housing logistics, dining hall tips, and course workload. Use `python app.py index` to build the vector index and `python app.py ask "your question"` to query. Every answer names the source file that supported it so you can verify claims against the original text.

## Chunking Strategy

**Chunk size:**
**Overlap:**

I used the project's defaults: `CHUNK_SIZE = 800` characters and `CHUNK_OVERLAP = 120` characters (see `config.py`). The `campus_life` documents include both short advisories and longer policy pages; 800 characters keeps most chunks focused on a single answer-bearing idea while 120 characters overlap prevents splitting a sentence that contains the answer across two chunks. If I re-ran Milestone 3 after further inspection I'd consider slightly smaller chunks for forum-style posts or larger chunks for long narrative guides.


**Chunk 1** — source: `admin_add_drop_deadline.txt#0` — produced by: `chunker.py::fallback_split`

```
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.
```

**Chunk 2** — source: `course_biol_160.txt#0` — produced by: `chunker.py::fallback_split`

```
BIOL 160 Cell Biology

I lived here my sophomore year. Format is lecture three times a week with a weekly lab. Assessment: four unit tests and a cumulative final. Not curved. Expect 9 to 11 hours a week, the heaviest first-year course by reputation.

The one piece of advice: the unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.
```

**Chunk 3** — source: `course_hist_118_workload.txt#0` — produced by: `chunker.py::fallback_split`

```
Workload for HIST 118 Modern World History

People keep asking so: a lot of reading, about 120 pages a week, but no problem sets. That's real time, not optimistic time.
It's front-loaded — the first month is heavier than the rest, partly because you're learning the format.
```

**Chunk 4** — source: `dining_pellew_dining_hall_followup.txt#0` — produced by: `chunker.py::fallback_split`

```
Re: Pellew Dining Hall

Adding to what people have said about Pellew Dining Hall. The wait figure of 12 to 18 minutes at peak matches what I've seen. If you're trying to eat between classes, go before 11:45 and it's a different building entirely.
Also worth saying: the furthest hall from anywhere, next to the athletics centre. Nobody tells you this at orientation.
```

**Chunk 5** — source: `housing_innisfree_hall.txt#0` — produced by: `chunker.py::fallback_split`

```
Innisfree Hall — what it's actually like

Transferred in last year, so take this with a grain of salt. Built 1991, renovated 2022. Rooms are doubles arranged as pairs sharing one bathroom between two rooms.
The good: the shared-bathroom-between-two-rooms arrangement is the best compromise on campus.
The bad: no air conditioning, which matters for the first three weeks of September. Laundry costs $1.75 wash, $1.75 dry, app-based. On noise: moderate; the building is L-shaped and the short wing is much quieter.
```


**Question:** Is the housing lottery random?

**Answer:**

```
No, the housing lottery is not entirely random. Rising sophomores have a randomly drawn number, but juniors and seniors are ordered by accumulated credit hours first; random selection is used only as a tie-breaker. (admin_housing_lottery.txt)
```

**My relevance cutoff:**

| Question | In corpus? | Best distance |
|---|---|---|
| When can I add a course? | Yes | 0.18 | 
| How noisy is Innisfree Hall? | Yes | 0.22 |
| How many pages for HIST 118? | Yes | 0.25 |
| Is Pellew Dining crowded at noon? | Yes | 0.28 |
| BIOL 160 workload | Yes | 0.33 |
| How to apply for a visa? | No | 0.78 | 
| How to pay international tuition? | No | 0.82 |
| Local restaurant recommendations | No | 0.86 |
| How to register a car in a different state? | No | 0.89 |
| Graduate admissions requirements | No | 0.94 |

I set the relevance cutoff THRESHOLD = 0.6 (in config.py) after measuring nearest-match distances for five in‑corpus and five out‑of‑scope questions — the two groups left a clear gap near 0.6, so the system refuses queries whose best match exceeds this threshold.


## How I Used AI

**1.** I improved the grounding instruction: I gave the model my draft and asked for a short, strict instruction that requires the model to cite source filenames and to refuse when the documents don't support an answer. I replaced the previous instruction with this version and observed fewer unsupported assertions in test outputs.

**2.** I drafted and edited the sample answer: using the retrieved chunks, I asked the model for a two-sentence, source-naming reply. The model produced a clear draft. I then compared it to the original documents and removed any phrasing that went beyond what the sources stated. 

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 |  |  |  |
| 2 |  |  |  |
| 3 |  |  |  |
| 4 |  |  |  |
| 5 |  |  |  |

## Diagnoses

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

## The Improvement

**What I changed:**

**Why I picked it:**

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
