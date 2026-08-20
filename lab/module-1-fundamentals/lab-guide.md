# Lab Guide — Module 1: GenAI Fundamentals & AI-Assisted PM Workflows

**Duration:** ~45 minutes hands-on
**Tools:** VS Code + Copilot Chat only (no MCP, no live Jira needed)
**Files you'll use:** everything in `sample-data/`

> Reminder: every output here is a **draft for human review**, not a
> report you send as-is. Part of the exercise is noticing where Copilot gets
> it wrong or invents detail not in the source files.

---

## Exercise 1 — Foundational prompt patterns (10 min)

Open `sample-data/sprint-board-export.csv` and `sample-data/backlog.csv` in
VS Code so Copilot Chat has them in context (`#file` reference them, or select
+ "Add to Chat").

Try these three prompts in order and notice how the output improves:

**Attempt 1 (no context):**
> "Summarize the sprint."

**Attempt 2 (add context):**
> "Using #sprint-board-export.csv, summarize this sprint's progress: how many
> story points are done vs. in-progress vs. not started, and which items are
> at risk of missing the sprint."

**Attempt 3 (add clarity + format):**
> "Using #sprint-board-export.csv, write a 5-bullet sprint status summary for
> a program stakeholder audience. Bullet 1: overall % complete by story
> points. Bullet 2: what's done. Bullet 3: what's in progress. Bullet 4: any
> item with status 'Blocked' — call it out explicitly. Bullet 5: one-sentence
> risk callout if velocity looks off track. Do not invent information not in
> the file."

**Discussion:** what changed between attempts 1 → 3? This is the
context → clarity → iteration pattern used for the rest of the day.

---

## Exercise 2 — Generate a status report (15 min)

Using `sprint-board-export.csv` **and** `backlog.csv`, prompt Copilot Chat to
draft a stakeholder-ready status report. Include:

- Sprint goal progress (from the sprint board)
- Backlog health (from the backlog file — how much is groomed/ready vs. not)
- One risk or blocker, cited from the data (not invented)
- A "what's next" line for the upcoming sprint

> **Expect this first attempt to already look reasonably solid** —
> especially on a capable model. The CSVs alone have Blocked-status items
> and a Critical defect, so "one risk, cited from the data" is satisfiable
> without the meeting notes. If Attempt 1 already reads well, that's not a
> sign something's wrong with the exercise — it's the point of Exercise 1's
> context→clarity pattern working.

Now open `sample-data/team-meeting-notes.md` and re-run the prompt, this
time asking Copilot to **fold in relevant risk signals from the meeting
notes** (e.g. a blocked dependency, someone on leave, a flaky test
environment). Compare the two reports — don't expect a night-and-day
difference. Look specifically at the risk/blocker line: a CSV-only report
can only say *"QAM-205 is Blocked"*; a report that's also seen the meeting
notes can say *why* (bench-rig signal noise, blocked on INF-88) and *since
when*. That precision — not the mere presence of a risk callout — is what
multi-source context buys you, and it's what a stakeholder actually needs
to act on the information rather than just be told a status.

---

## Exercise 3 — Risk review + retrospective summary (10 min)

Using only `team-meeting-notes.md`:

1. Ask Copilot to **surface every risk mentioned across the notes**, grouped
   by severity (High/Medium/Low), with the source line quoted for each —
   this citation habit matters for the responsible-AI theme in Module 3.
2. Ask Copilot to draft a **sprint retrospective summary** (What went well /
   What didn't / Action items) from the same notes.

---

## Exercise 4 — Refine a weak prompt into a reusable template (10 min)

Open `sample-data/weak-prompt-example.md`. It contains a real weak first
attempt at a status-report prompt, plus a note on why it underperforms.

1. Run the weak prompt as-is against `sprint-board-export.csv` — see the poor
   output.
2. Rewrite it as a **reusable template** with placeholders (audience, data
   source, sections required, tone, explicit "don't invent data" guardrail).
3. Save your version — this is the pattern you'll reuse for recurring agile
   ceremonies (backlog grooming, retro summaries) going forward. Compare
   against `../prompt-template-library.md` afterward to see the reference
   version.

---

## Wrap-up questions (facilitator-led — fold into the last few minutes of Exercise 4, no separate slot)

- Where did Copilot add something plausible-sounding that wasn't in the
  source data? What would have caught that before it reached a stakeholder?
- What's the smallest amount of context that got you a usable answer?
