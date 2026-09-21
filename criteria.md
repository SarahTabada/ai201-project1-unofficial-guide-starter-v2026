# Acceptance criteria — The Unofficial Guide

Five criteria that say what "working" means for this system, written in unit 1
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"Retrieval works"* is an opinion. *"For at
least 4 of my 5 test questions, the top results include a chunk containing the
answer"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter or looser one. A reason that says something about your corpus or your
pipeline earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## 1. Retrieved chunks contain the answer

For at least 4 of my 5 test questions, the retrieved chunks include one that
contains the answer.

**Why this target:**
<!-- e.g. "One of my questions is about a topic only two documents mention, so
     I expect that one to be hard." -->
This target focuses evaluation on retrieval quality, which is the stage most
likely to fail in a grounded system. If a relevant chunk isn't retrieved,
the generator has nothing truthful to work with. Allowing one miss (4 of 5)
acknowledges natural edge cases (short or unusually phrased answers) while
still requiring retrieval to be reliable for the majority of questions.

---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**
<!-- Why all five and not four? What about your setup makes that achievable —
     or what would have to go wrong for it not to be? -->
Source attribution is the core safety mechanism for this assignment: it
lets a reader verify claims and detect hallucinations quickly. The system's
prompting and grounding design explicitly require filenames in the answer,
so naming a source for all answers is an achievable and testable requirement
that enforces accountable outputs.

---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" —
in at least 4 of 5 tries.

<!-- The five questions are the ones in `OUT_OF_SCOPE` at the bottom of
     `questions.py`, and `run_eval.py` puts them through the gate and writes
     what happened into your run log. Swap them for your own if you'd rather —
     just keep five of them, or the "4 of 5" above has nothing to be 4 of. -->

**Why this target:**
<!-- What did your distances look like when you set the cutoff in Milestone 4?
     Was there a clean gap, or did the two groups overlap? -->
The gate is the primary defense against off-topic model calls and hallucination.
Requiring a 4 of 5 refusal rate balances occasional borderline cases (where
an out-of-scope question superficially matches a noisy document) with the
need to avoid wasting API quota and producing unsupported answers. I chose
this because the distance distributions for in-corpus vs out-of-scope
questions usually show a gap but not a perfect separation, so a 4/5 target
is realistic and meaningful.

---

## 4. Something about your chunks

<!-- YOU WRITE THIS ONE.

     How would you know if your chunks were the right size? Name something
     countable or observable.

     Examples of the right shape — don't copy these, they should come from
     what you actually saw in Milestone 3:
       - "At least 4 of 5 sampled chunks read as a complete thought, with no
          sentence cut in half at either end."
       - "No chunk is shorter than 200 characters, since anything below that
          in my corpus turned out to be a heading with no content under it." -->


At least 4 of 5 sampled chunks read as a complete thought, with no sentence
cut in half at either end.

**Why this target:**

When a chunk splits a sentence, retrieval frequently returns fragments that
cannot answer a question by themselves; keeping most chunks as complete
thoughts increases the chance a single retrieved chunk contains an answer and
makes grounding straightforward to evaluate.

---

## 5. Your choice

<!-- YOU WRITE THIS ONE TOO.

     Pick something you actually care about getting right. It could be about
     speed, about refusals, about a particular kind of question your corpus
     handles badly, about source attribution being correct rather than merely
     present — anything, as long as it names a number or an observable
     outcome. -->


For at least 4 of my 5 test questions, the generated answer is concise (no
more than three sentences) and contains the short expected token/phrase I
listed in `questions.py`.

**Why this target:**

Grounded answers should be brief and directly cite information from retrieved
chunks; measuring both concision and presence of the expected token is a
practical way to detect rambling or hallucinated replies while keeping the
check automatable for unit 2 evaluation.


---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 2 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 1. Retrieved chunks contain the answer

         For at least 4 of my 5 test questions, the retrieved chunks include
         one that contains the answer.

         **Why this target:** ...

         > **Revised in unit 2:** For at least 4 of 5 questions, the top three
         > results contain the answer.
         >
         > **Why revised:** I couldn't judge "the chunks include one that
         > contains the answer" the same way twice — I scored two questions
         > differently on Monday than on Wednesday. The new version is
         > something I can actually check.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said 4 of 5 but got 2 of 5, so 2 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.

     The whole reason the originals stay visible is so someone can see what you
     said before you knew the answer.
     ───────────────────────────────────────────────────────────────────────── -->
