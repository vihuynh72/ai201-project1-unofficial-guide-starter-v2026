# The Unofficial Guide

<!-- Replace this line with your name and which corpus you picked. -->

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does

I chose the `campus_life` corpus, which contains 88 short posts about courses, housing, dining, transit, and campus services. The system searches these posts for information related to a student's question, checks whether the closest result is relevant, and writes a short answer using the retrieved text. It can answer specific questions, such as when west-lot permits go on sale or how far ahead study rooms can be booked. If the relevance check finds no close match, the system refuses to answer.

## Chunking Strategy

**Chunk size:** One complete source document per chunk, 178 to 549 characters in this corpus.
**Overlap:** 0 characters.

The `campus_life` corpus has 88 short posts, averaging 317 characters, and useful information often sits in one sentence. I kept each post whole so its topic and campus context stay beside the details. This produces 88 chunks, one for each source document.

## Sample Chunks

**Chunk 1** (source: `admin_add_drop_deadline.txt`; produced by: `chunker.py::split_documents`)

```
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.
```

**Chunk 2** (source: `course_biol_160.txt`; produced by: `chunker.py::split_documents`)

```
BIOL 160 Cell Biology

I lived here my sophomore year. Format is lecture three times a week with a weekly lab. Assessment: four unit tests and a cumulative final. Not curved.

Expect 9 to 11 hours a week, the heaviest first-year course by reputation.

The one piece of advice: the unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.
```

**Chunk 3** (source: `course_hist_118_workload.txt`; produced by: `chunker.py::split_documents`)

```
Workload for HIST 118 Modern World History

People keep asking so: a lot of reading, about 120 pages a week, but no problem sets. That's real time, not optimistic time.

It's front-loaded — the first month is heavier than the rest, partly because you're learning the format.
```

**Chunk 4** (source: `dining_pellew_dining_hall_followup.txt`; produced by: `chunker.py::split_documents`)

```
Re: Pellew Dining Hall

Adding to what people have said about Pellew Dining Hall. The wait figure of 12 to 18 minutes at peak matches what I've seen. If you're trying to eat between classes, go before 11:45 and it's a different building entirely.

Also worth saying: the furthest hall from anywhere, next to the athletics centre. Nobody tells you this at orientation.
```

**Chunk 5** (source: `housing_innisfree_hall.txt`; produced by: `chunker.py::split_documents`)

```
Innisfree Hall — what it's actually like

Transferred in last year, so take this with a grain of salt. Built 1991, renovated 2022. Rooms are doubles arranged as pairs sharing one bathroom between two rooms.

The good: the shared-bathroom-between-two-rooms arrangement is the best compromise on campus.

The bad: no air conditioning, which matters for the first three weeks of September.

Laundry costs $1.75 wash, $1.75 dry, app-based. On noise: moderate; the building is L-shaped and the short wing is much quieter.
```

## Sample Answer

**Question:**
When do student permits for the west lots go on sale?

**Answer:**
Student permits for the west lots go on sale in August (admin_parking_permits.txt).

**Source:** `admin_parking_permits.txt`

**My relevance cutoff:** `0.6`

The five in-scope questions had best distances from 0.1847 to 0.4560. The five out-of-scope questions ranged from 0.8246 to 0.9340. I kept 0.6 because it is above the furthest in-scope result and below the closest out-of-scope result. All ten questions fell on the expected side of the cutoff.

| Question | In corpus? | Best distance |
|---|---|---|
| When do student permits for the west lots go on sale? | Yes | 0.2190 |
| How much does an official transcript cost? | Yes | 0.1847 |
| How often does the shuttle goes between campus on the weekdays? | Yes | 0.4218 |
| How advance can I book a study room? | Yes | 0.3330 |
| Does the laundry machine in Old Brewhouse accept card? | Yes | 0.4560 |
| What is the capital of Mongolia? | No | 0.8246 |
| How do I change the oil in a diesel engine? | No | 0.9340 |
| Who won the 1994 World Cup? | No | 0.8859 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.8442 |
| How do I write a for loop in Rust? | No | 0.8960 |

## How I Used AI

I asked AI to help me understand the concepts and guide me thru which file to look at.

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

## Run Log — Before

I ran all five questions three times each. My first attempt gave bad numbers because the search index was out of date. I threw that run away, rebuilt the index, and ran again. I also fixed a small bug in my answer checker. It only matched answers that used the exact same capital letters as my expected text.

Criteria 3 and 4 do not change from run to run, so I measured them once and put the same number in all three columns.

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 2. Every answer names a source | 5 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 4. Sampled chunks make sense on their own | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET (one close call) |
| 5. Main claim is supported by a retrieved source | 5 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |

Two real answers from the first run:

```
An official transcript costs $8. 

Source: admin_transcript_requests.txt
```

```
No, the laundry machines in Old Brewhouse are coin only. 

Source: housing_old_brewhouse.txt (and housing_old_brewhouse_laundry.txt)
```

The gate refused all five out-of-scope questions. Their best distances were 0.825, 0.934, 0.886, 0.844 and 0.896. The cutoff is 0.6.

For criterion 4, I picked five chunks at random. Four were clearly fine. The fifth was a dining follow-up post. It starts with "Adding to what people have said about The Atrium." It names the place and its facts make sense, so I passed it. But it refers to a post the reader has not seen.

## Verdicts

MET means I hit my target. MISSED means I did not. I kept every target from unit 1.

| # | What I checked | Target | Runs | Verdict | Why |
|---|---|---|---|---|---|
| 1 | The search finds the chunk with the answer | 4 of 5 | 5, 5, 5 | MET | The answer was in the results every time. |
| 2 | Every answer names a source | 5 of 5 | 5, 5, 5 | MET | All 15 answers named one. |
| 3 | The system refuses off-topic questions | 4 of 5 | 5, 5, 5 | MET | It refused all five. |
| 4 | Chunks make sense on their own | 4 of 5 | 5, 5, 5 | MET | Close. One chunk barely passed. Without it, I still hit 4 of 5. |
| 5 | The named source supports the answer | 5 of 5 | 5, 5, 5 | MET | Each named source held the fact. |

- Criterion 3 is the weakest. My off-topic questions were easy, like the capital of Mongolia. I also tried two campus questions that the documents do not answer: gym prices and bookstore hours. The system did not refuse them. I kept MET because my target was about clearly off-topic questions.
- Criterion 4 relies on my opinion. It still holds if I fail the close chunk.
- Criteria 1, 2 and 5 are checked by matching text, so my opinion does not change them.

I did not change any criterion.

## Diagnoses

I missed nothing, so no question failed that I could trace back to a stage.

But my targets were easy. Each test question has one clear answer in one short post. So I tried harder questions. Searching costs nothing, so I could try many.

**Campus questions the documents don't answer.** I asked five: gym prices, bookstore hours, pharmacy, student clubs and the career fair. The system should refuse all five. It refused three. The gym question (0.584) and the bookstore question (0.519) slipped under the 0.6 cutoff.

**A question the documents do answer.** "How do I see a counselor?" scored 0.725, so the system would refuse it. But the health center post covers counselling. Spelling it "counselling" helped only a little (0.636). "How long is the wait for counselling?" scored 0.376. My best guess is that the post is mostly about walk-in hours, so a broad "how do I" question lands far from it.

**The pattern.** Both problems come from the search step (embedding and retrieval). The gate trusts one distance number. That number shows how close the topic is. It does not show whether the answer is there. Unanswered campus questions scored 0.52 to 0.63. Answered but broad questions scored up to 0.73. The two ranges overlap, so no single cutoff can fix both.

**What I would tighten.** Criterion 3 would become: "The system refuses at least 4 of 5 campus questions the documents don't answer." Today it gets 3 of 5, so that version would be a miss.

I also found a smaller problem. Seven dining follow-up posts start with "Re:" and repeat facts from the main post. They crowd the search results. I fix this below.

## The Improvement

**What I changed:** Each "Re:" follow-up dining post is now joined to its main post. Chunks went from 88 to 81. Unit 1 above still says 88. I left it as I wrote it.

**Why I picked it:** The follow-ups lost their context and filled the top search results. Joining them fixes both.

### Run Log — After

Same five questions, three runs each, after rebuilding the index.

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 2. Every answer names a source | 5 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 4. Sampled chunks make sense on their own | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 5. Main claim is supported by a retrieved source | 5 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |

**Did it help?**

Not on my five questions. They already passed everything, so the numbers did not move. It did clean up the search results, and nothing got worse.

- Every distance score is the same before and after.
- Follow-up chunks went from 7 to 0.
- When I asked about the wait at Kestrel Commons, four of the top five results used to be follow-ups. Now Kestrel Commons is first and the rest are main posts.

I tried only six extra searches. I judged them by the names of the results, not by the answers. So this shows cleaner results, not better answers.

## What's Still Broken

- The gate lets through campus questions that the documents do not cover. A gym membership question scored 0.584 and a bookstore question scored 0.519, both under the 0.6 cutoff. My five out-of-scope questions were too easy to show this. I did not try to fix it.
- Five questions that all pass is a weak test. I would add questions where two similar posts compete. For example, five laundry posts came back for the Old Brewhouse question. I stopped because the targets I set in unit 1 are met.
- My answer checker only looks for the expected text in the answer. It does not check the source. I measured criteria 1, 2 and 5 with a separate quick script.
- Each chunk is a whole post. A long post could hide a fact. All posts here are short, so it did not happen. I have not tested longer ones.

## What I'd Do Differently

I would change criteria 4 and 5. For criterion 4, I would check every chunk, not a sample of five. The bad ones are rare, and a sample will miss them. Criterion 5 overlaps with criterion 2 when only one post holds the answer. I would rewrite it to cover questions that need two posts, or where two posts disagree.
