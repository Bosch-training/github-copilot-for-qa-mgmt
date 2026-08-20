# Optional Fallback Data (not a shared sandbox)

Module 2 is designed to run against **your own real Jira project** — see
`00-setup/setup-guide.md` §3. These three CSVs are synthetic and exist for
two narrower purposes:

1. **You don't have a suitable real project** (no active/recent sprint, or
   nothing you're allowed to point Copilot/an AI provider at) — import these
   into a personal sandbox project in your own Jira instance instead.
2. **Reference** — seeing the shape/fields the exercises expect, without
   needing to touch Jira at all yet.

Nobody pre-seeds a shared site for you this time. If you use this data,
you're importing it into a project only you can see.

## Steps (if you're using the fallback)

1. In your own Jira Cloud instance, create a personal/sandbox project —
   any key works, but if it isn't `QAM`, find-and-replace the key prefix
   across all three CSVs first (or just point the exercises' JQL at
   whatever key you used).
2. Jira Settings → System → **External System Import** → CSV.
3. Import in this order (defects link to sprint issues by key, so issues
   should exist first):
   1. `sprint-issues.csv`
   2. `backlog.csv`
   3. `defects.csv`
4. Map columns to fields as named (Summary → Summary, Type → Issue Type,
   Status → Status, Assignee → Assignee — Jira will prompt to create these
   users or leave unassigned, Story Points → your org's story point custom
   field, Sprint → Sprint, Priority → Priority).
5. **Confirm the Story Points custom field ID after import** (Jira admin →
   custom fields, or inspect one issue via the API) and update it in
   `../n8n-workflow-starter.json`'s "Format Issues For Prompt" Code node,
   which currently hardcodes `customfield_10016` — a common default, not
   guaranteed to match your instance. If it's wrong, the workflow doesn't
   break (there's a `?? 'n/a'` fallback), it just silently shows no points
   in the AI summary.
6. `Linked Requirement` (in `defects.csv`) references AEB requirement IDs
   used in Module 3 — either map it to a custom text field or a Jira label;
   exact match isn't critical for Module 2, but keep the values intact if
   you want the Module 3 cross-references (see below) to make sense.
7. Update the n8n workflow's Jira node JQL and this project's key/sprint
   name to match whatever you actually created.

## The one narrative thread, if you're using this fallback data

`defects.csv` row **QAM-315** ("Radar multipath reflection triggers false
AEB activation at highway speed") is the same defect Module 1's Sprint 14
retro flags as found during triage (there, tracked as QAM-207) — still open
going into Sprint 15, and exactly what Module 3's PR scope-check ticket
(AEB-99) fixes. Worth pointing out if you did Module 1 the same day — it's
the one thread running through all three modules. It only holds together if
you're using this synthetic fallback data; a real Jira project obviously
won't have it.

## Resetting your own sandbox

If you re-run this exercise more than once, bulk-delete and re-import
rather than importing on top of stale data — old n8n workflow executions
against the same project will otherwise skew the defect-density numbers
you compute in Part A.
