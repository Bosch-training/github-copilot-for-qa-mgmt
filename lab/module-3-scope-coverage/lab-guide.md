# Lab Guide — Module 3: PR Scope Check, Coverage-Gap Check & Responsible AI Governance

**Duration:** ~60 minutes hands-on (Exercise 1 ~20 min, Exercise 2 ~25 min,
governance discussion ~15 min)
**Tools:** VS Code + Copilot Chat only (no MCP/n8n needed)
**Files you'll use:** everything in `sample-data/`

---

## Exercise 1 — PR scope check (20 min)

Open `sample-data/sample-pr/ticket-AEB-99.md` and
`sample-data/sample-pr/pr-diff.md`.

Prompt Copilot Chat:

> "Using #ticket-AEB-99.md as the intended scope and #pr-diff.md as the
> actual change, assess whether the PR matches the ticket's scope. List each
> distinct change in the PR, mark it In Scope or Out of Scope against the
> ticket's acceptance criteria, and flag anything that looks like it could
> break another system silently. Cite the specific diff hunk for each
> finding."

Work through it yourself first — form your own opinion on which changes are
scope creep before reading Copilot's answer. Then compare:

1. Did Copilot catch all four changes in the diff (rate limiter fix, token
   expiry change, response-contract change, deleted legacy route)?
2. Did it correctly flag which one is in-scope?
3. Did it reason about *why* the out-of-scope changes are risky (not just
   that they're unrelated)?

When done, open `answer-keys/pr-scope-answer-key.md` and compare against
your own read plus Copilot's. Note any gap between the three.

---

## Exercise 2 — Coverage-gap check (25 min)

Open `sample-data/aeb-requirements.csv`,
`sample-data/test-execution-report.xml` (or the `.html` version — same
data), and `sample-data/execution-log.txt`.

Prompt Copilot Chat:

> "Using #aeb-requirements.csv as the full requirement list and
> #test-execution-report.xml as this build's test results, produce an
> advisory coverage-gap report with two sections: (1) requirements with NO
> corresponding test case in this build, and (2) requirements that have a
> test case but it failed. For each item, include the requirement ID,
> title, and priority. This is advisory only — flag it as needing human
> confirmation before being treated as a real gap."

Then extend it:

> "Cross-reference #execution-log.txt for the failing tests — does the log
> suggest an environment issue (e.g. infra/timing) or a genuine functional
> defect? Note your confidence for each."

**Work individually.** When you have your own list, open
`answer-keys/coverage-gap-answer-key.md` and self-score:

- Did you (and Copilot) find all the zero-coverage requirements?
- Did you distinguish "no test exists" from "test exists but failed" — these
  need different follow-up actions and it's a common conflation.
- Where did the log cross-reference change your confidence in a finding?

---

## Responsible AI Governance discussion (15 min, facilitator-led)

Using both exercises as concrete anchors, discuss:

1. **Where in Exercise 1 or 2 would you *not* trust the AI output without a
   human re-check before it reached a release-go/no-go decision?**
2. **What's the difference between "advisory" and "authoritative" in your
   own team's workflow?** Where's the line for status reports vs. release
   decisions vs. security findings?
3. **Governance checklist** — for any GenAI-assisted output your team relies
   on, can you answer:
   - What source data did it use, and is that traceable?
   - Who reviews it before it's acted on, and how do they know to look
     critically rather than rubber-stamp?
   - What happens if the AI is wrong — what's the blast radius?
4. Wrap-up Q&A.
