# Prompt Template Library — GenAI for Test Management

Reference sheet of every reusable prompt pattern taught across the three
modules. Hand this out at the end of the day as the "take back to your desk"
artifact — it's the payoff for the whole prompt-refinement exercise in
Module 1.

Every template shares the same shape on purpose: **source → audience/format
→ required sections → guardrail.** That shape is the actual lesson; the
specific templates are just applications of it.

---

## 1. Status report generator

```
Using {data_source}, write a status report for {audience}.
Include:
- Overall progress (% complete by story points or equivalent)
- What's done this period
- What's in progress
- Any blocked/at-risk items, called out explicitly
- One-sentence risk callout if progress looks off track
Tone: {tone, e.g. "concise, exec-facing"}
Constraint: only use information present in {data_source}. If something
isn't in the data, say "not available" rather than inferring it.
```

## 2. Risk review from meeting notes / backlog

```
Using {data_source}, surface every risk, blocker, or dependency concern
mentioned. Group by severity (High/Medium/Low) using your judgment on
impact and likelihood, and quote the source line for each so it's
traceable. Do not invent risks not evidenced in the source.
```

## 3. Sprint retrospective summary

```
Using {data_source}, draft a retrospective summary with three sections:
What went well, What didn't go well, Action items (with an owner if one is
named in the source). Keep each bullet to one sentence. Only include items
with evidence in the source data.
```

## 4. Backlog grooming assistant

```
Using {backlog_source}, flag items that: (1) have no story point estimate,
(2) are marked "Needs Grooming" and haven't been touched in {N} sprints if
that data is available, (3) look duplicative based on similar summaries.
List each with its ticket ID and a one-line reason.
```

Practiced directly in Module 1, Exercise 5.

## 5. Copilot-assisted JQL (via MCP)

```
Write a JQL query for {criteria in plain English}. Explain what the query
does in one sentence before running it, so I can sanity-check it. Then run
it via your org's MCP server.
```

Habit to reinforce: always ask for the explanation, not just the query —
this is what makes "Copilot-assisted" different from "Copilot-trusted."

Extend it when you need ranked or analyzed results, not just a filtered
list — same shape, one more clause:

```
Write a JQL query for {criteria}. Explain it, then run it via your org's
MCP server, and rank the results by {ranking criterion, e.g. "how long
each has been in its current status"}.
```

## 6. Coverage-velocity / defect-density synthesis

```
Using live sprint, backlog, and defect data pulled via MCP, produce a
report with:
1. Sprint velocity — points done vs. committed
2. Defect density — open defects per 10 story points delivered this sprint
3. Backlog health — % of backlog items in "Ready" status
4. One flagged risk if defect density looks high relative to velocity
Cite the specific tickets behind each number.
```

## 7. PR scope check

```
Using {ticket} as the intended scope and {pr_diff} as the actual change,
list each distinct change in the PR and mark it In Scope or Out of Scope
against the ticket's stated acceptance criteria (and explicit out-of-scope
notes, if present). For out-of-scope items, flag the risk of bundling them
into this PR. Cite the specific diff hunk for each finding. This is
advisory — a human should confirm before requesting changes.
```

## 8. Coverage-gap check

```
Using {requirements_source} as the full requirement list and
{test_results_source} as this build's results, produce a two-section
report: (1) requirements with NO corresponding test case, (2) requirements
with a test case that failed. Include requirement ID, title, and priority
for each. If a log file is available, cross-reference failures against it
and note whether each looks like an environment issue or a likely
functional defect, with your confidence level. Flag this as advisory,
needing human confirmation before being treated as a real gap.
```

## 9. Test execution summary deck (with defect linkage)

```
Using {test_results_source} (execution report/log) and {defect_source} as
the defect tracker export, for the {system/use case, e.g. "ADAS AEB"}:
1. Classify every test case by result: Passed / Failed / Not Executed
   (a requirement with no corresponding test case counts as Not Executed).
2. For each Failed test, state the failure reason and check whether a
   defect is already linked in {defect_source}; if none exists, flag it as
   an orphan failure needing a new defect.
3. For each Passed test, confirm from the execution log that it ran the
   correct number of iterations with no retries, so the pass can be
   trusted; flag any pass that required a retry.
4. Summarize briefly (bulleted, exec-facing) and include a donut chart of
   the Passed/Failed/Not Executed split.
Constraint: only use what's in the named sources; say "not available"
rather than inferring; cite the specific ticket/requirement ID for every
claim.
Output format: {output format, e.g. "pptx"}. Filename: {naming rule, e.g.
"today's date"}. Output folder: {target folder}.
```

---

## The pattern underneath all nine

```
Using {named data source(s)} —
  write/produce {specific artifact} for {specific audience}.
  Include: {explicit, enumerated sections}.
  Constraint: only use what's in the source; say "not available" rather
  than inferring; cite/quote where possible.
```

If a prompt in the wild isn't getting good output, the fix is almost always
"which of these four slots is missing or vague" — not a longer prompt.
