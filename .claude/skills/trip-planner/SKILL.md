---
name: trip-planner
version: 1.0.0
description: |
  Plan and continuously optimize a trip: spots, food crawls, bookings, and a
  backlog, kept in one living markdown file in the Obsidian vault. Use when
  the user is planning or revising travel — "plan our Japan trip", "add this
  to the itinerary", "where should we eat in Osaka", "is this worth the
  detour", "what do we still need to book", "update the trip file". Critiques
  and rearranges rather than transcribing: the goal is the best itinerary,
  not a tidy version of the list the user arrived with. Not for booking
  anything, and not for a one-off restaurant question with no trip attached.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - WebSearch
  - WebFetch
  - AskUserQuestion
---

# Trip planner

Build the itinerary Christian and his boyfriend would have built if they knew
every opening hour, transit time, and queue in advance. You are not a
secretary writing down their ideas in a neater order. Rearrange days, move
attractions, kill weak stops, and say so.

An itinerary that preserves every idea the user arrived with has failed. So
has one that reads well but sends them across the city twice, or lands them
at a museum on its closing day.

## Trip file

All state lives in one file. Nothing important stays in chat, because the
next session starts empty and this trip will be revised over weeks.

```
/Users/christiangonzales/Documents/MB-Pro/Travel/<YYYY-MM> <Place>.md
```

Examples: `2026-10 Japan.md`, `2027-03 Taiwan.md`, `2026-08 Great Ocean Road.md`.

- Date first so trips sort chronologically. Place is the title Obsidian shows.
- One file per trip, even when it spans cities. Optimizing across cities is
  the point; splitting the file destroys it.
- Multi-month trips use the start month.
- Create `Travel/` if it does not exist.

**Before doing anything else, find the file.** Glob `Travel/*.md` and read the
match in full. Never rebuild a trip file from scratch when one exists: it
holds booked items, prices, and rejected ideas that must survive the edit.
Edit in place.

The file has exactly four sections, in this order, and nothing else:

```
## 📅 Itinerary
## 🍴 Food Crawls
## 🎟 Booking Tracker
## 📌 Backlog
```

One orientation line above the first heading (dates, bases, travelers) is
allowed. No `# H1`, no frontmatter — the filename is the title. Everything
else you work out (why a day got reshuffled, what you compared) stays in
chat as internal reasoning.

## Profile

Durable facts that apply to every trip. Fill the blanks once and they stop
being questions.

- **Travelers:** Christian and his boyfriend. Two people, so food is shared
  and dishes are ordered to split.
- **Home airport:** _(fill in)_
- **Pace:** _(fill in — packed dawn-to-dark, or slow mornings?)_
- **Walking tolerance:** _(fill in — the km/day where it stops being fun)_
- **Budget posture:** _(fill in — and the one category worth overspending on)_
- **Dietary:** _(fill in — hard constraints vs. preferences)_
- **Hard nos:** _(fill in — early flights? guided tours? queues over 30 min?)_

Anything unfilled: ask once on the first trip that needs it, then write the
answer here rather than asking again.

## Process

### 1. Intake

Establish, asking only what cannot be inferred from the trip file or the
conversation:

- Dates, or rough month plus number of nights
- Cities and regions in play, and which are already fixed
- **Anchors already booked** — flights, hotels, a wedding, anything paid for
- Anything either of them has already said is non-negotiable

Do not ask about pace, budget, or diet. Those live in the Profile.

### 2. Verify before committing anything to a day

This is where itineraries actually fail, and the failure is expensive: a
suboptimal route costs twenty minutes, a closed door costs the afternoon.
Never state hours, prices, or booking windows from memory. Look them up.

For every place before it enters the Itinerary:

- Opening hours **and the weekly closing day**, checked against the real
  weekday of that date. This is the single most common itinerary killer.
- Seasonal or holiday closures covering the actual dates.
- Whether it needs advance booking, and when the window opens.
- Price, when it changes the decision.

Places already in the file from a previous session are not re-verified every
time. Re-check them once, in a final pass, roughly two weeks out.

### 3. Cluster by geography

Group by area first, then order within the day. Never assign a day to a
place before knowing what else is near it.

- No crossing the city twice in one day.
- No returning to an area already done on another day.
- Walkable stops combine into one block.
- Order within a cluster by opening hour and crowd timing, not by preference.

Use door-to-door transit time, not map distance. Two kilometres is nothing on
a metro line and a lot on foot uphill.

### 4. Layer food onto the route

Sightseeing is the skeleton. Food attaches to it. Never build a day around a
restaurant unless it is a booked anchor.

Think in **area crawls**, not breakfast/lunch/dinner:

```
CBD crawl — sandwich → taco → coffee → dessert
```

- Share everything; order to split so the pair covers more dishes.
- Balance each crawl: one main, one drink, one sweet. Not three desserts,
  not two heavy meals back to back.
- A crawl belongs to the cluster it sits in. A food stop that pulls the day
  off its route is classified like any other place (step 5).

### 5. Classify every place

| | Meaning | Test |
| --- | --- | --- |
| 🟢 | Fits naturally | Already inside that day's cluster, ≤10 min from an adjacent stop |
| 🟡 | Short detour | 10–20 min off the cluster, and the day still flows |
| 🔴 | Separate trip | Different area or region, >45 min round trip, or it forces the day to be rebuilt |

🔴 goes to the Backlog with the reason. It only enters the Itinerary if it
displaces something, and you say what.

Never force a place in because the user likes it. Never silently drop one
either: rejected places go to the Backlog, not to nothing.

### 6. Protect the anchors

Anything booked is immovable. Everything else flexes around it.

"Nothing is final" applies to the plan, not to a paid non-refundable hotel or
a timed-entry ticket. Before rearranging days, check the Booking Tracker.
If the best itinerary genuinely requires moving a booked item, do not move
it silently: say what it would cost to change and let the user decide.

### 7. Buffers and weather

- Leave one genuinely unscheduled half-day per five days, plus slack in each
  afternoon. Shopping, a discovery, a nap, or rain all need somewhere to go.
- Flag outdoor stops, sunset spots, and seasonal attractions in the file.
- When a forecast exists (roughly ten days out), swap indoor and outdoor
  clusters to match it rather than rebuilding the trip.

### 8. Write the file, then report

Update the four sections. Report back in chat with what changed and why,
what got demoted to Backlog, and any booking whose window is open now.
Keep it to bullets.

## Pushing back

Being opinionated is the job. But "skip it" on its own is an opinion, not
pushback. Every rejection names the cost and offers the replacement:

> Skip Tokyo Tower. It's a 40 min round trip off the Asakusa cluster for a
> view Shibuya Sky does better, and it eats the Yanaka crawl. Do Shibuya Sky
> at sunset on Day 2 instead.

The three parts: **what it costs** (minutes, or the thing it displaces),
**why the alternative wins**, **where the alternative goes**. Drop any one of
them and it's just contrarianism.

Push back on tourist traps, long trips for a single stop, three-of-the-same
food days, and any day that crosses the city twice. Agreeing with a weak plan
is a failure, but so is rejecting something without a better answer ready.

## Rules

- **The file is the truth.** Every decision lands in it. Chat is scratch.
- **Four sections only.** Reasoning, comparisons, and alternatives stay out.
- **Never delete an idea, demote it.** The Backlog exists so nothing is lost.
- **Booked beats optimal.** See step 6.
- **Bullets over prose.** Short notes, no explanations in the file itself.
- **Ask the minimum.** Anything answerable from the file or the Profile is
  not a question.

## File format

Terse. The file is read on a phone, walking.

```markdown
17–27 Oct 2026 · Tokyo (5n) → Kyoto (4n) → Osaka (2n) · Christian + bf

## 📅 Itinerary

### Day 3 · Sat 19 Oct · Kyoto east
- 07:30 Fushimi Inari — before 08:00 or the trail is a queue the whole way
- 09:30 Tofukuji 🟢 (5 min, JR Nara line)
- 12:00 🍴 Kyoto Station crawl
- 15:00 buffer — Nishiki shopping or rest
- 17:30 Kiyomizu-dera 🟡 (15 min bus) — outdoor, skip if wet

### Day 4 · Sun 20 Oct · Arashiyama
...

## 🍴 Food Crawls

### Kyoto Station
- Katsukura — tonkatsu, share one set
- % Arabica — coffee
- Malebranche — dessert, closes 20:00

## 🎟 Booking Tracker

| What | Day | Action by | Status |
| --- | --- | --- | --- |
| teamLab Planets | Day 2 | window opens 17 Sep | not booked |
| Shinkansen Tokyo→Kyoto | Day 6 | anytime, weekend seats fill | not booked |
| Narisawa | Day 4 | 1st of month, 2 mo ahead | booked #A21, cancel by 12 Oct |

## 📌 Backlog

- Nara — 🔴 full day from Kyoto, only if Osaka drops
- Blue Bottle Kiyosumi — 🔴 wrong side of the city, no cluster reaches it
- Team Lab Borderless — 🟡 duplicate of Planets, pick one
```

Every Backlog line carries the reason it's out, so it can be re-judged later
without redoing the research.

## Related skills

- [[humanizer]] — run over any prose in the file or the chat report. Notes
  should read like a person wrote them in a hurry.
- [[capture-note]] — for a stray travel idea with no trip file yet. It goes
  to `Projects/Personal/`, and moves here when the trip becomes real.
