# Lab Materials — "GenAI for Test Management" (Track 2)

This folder holds everything needed to *run* the workshop, as distinct from the
slide deck (owned separately) and the course outline (`../course-outline/`).

## Structure

```
lab/
  00-setup/                     Pre-work sent to participants before Day 1
  module-1-fundamentals/        Prompt patterns, status report, retro summary
  module-2-mcp-n8n/             Live Jira via MCP + JQL, n8n scheduled workflow
  module-3-scope-coverage/      PR scope check, coverage-gap check, governance
  facilitator-notes.md          Timing, talking points, demo tips, pitfalls
  prompt-template-library.md    Reference sheet of every reusable prompt taught
```

Each module folder contains a `lab-guide.md` (participant-facing, hand out as-is)
and a `sample-data/` folder. For Modules 1 and 3 that data is the exercise
itself (offline, no live accounts). For Module 2 it's optional fallback/
reference only — see below.

## Status

| Piece | Status |
|---|---|
| Module 1 (offline, no external accounts) | Ready to use |
| Module 2 sample data (Jira CSVs — fallback only, see below) | Ready to use |
| Module 2 n8n Docker setup (`module-2-mcp-n8n/docker/`, Plan B path) | **Verified** — `docker compose up -d` pulls and boots `n8nio/n8n:latest` cleanly, confirmed against a running container (see docker/README.md for the owner-account first-run flow) |
| Module 2 n8n starter workflow (`n8n-workflow-starter.json`) | **Structurally verified** — imported via `n8n import:workflow` and round-tripped through the REST API against a live n8n 2.35.3 container; every node type/version/connection persists exactly as authored with no issues flagged. Fixed 2 real bugs this way (see facilitator-notes.md). **Still needs**: a run with real Jira/AI-provider/SMTP credentials — nobody has done that yet (trainer-only steps for a first SMTP test are in facilitator-notes.md) |
| Splunk demo (`module-2-mcp-n8n/docker/splunk-demo.md`) — client-facing, not a lab exercise | **Verified end-to-end** — booted, HEC token created via REST API, test event sent and confirmed searchable with the correct field-extraction query. Caught and fixed 3 real issues this way (Web UI is HTTP not HTTPS by default, HEC needs HTTPS, and `\| spath` is required for the JSON fields to be searchable) — see docker/README.md and splunk-demo.md |
| Module 3 sample data + answer keys | Ready to use |
| Course Resources / Pre-Read / Assessment (marked TBD in the outline doc) | Not yet built — out of scope for this pass, flagged for follow-up |

## How hands-on is delivered

Every exercise runs inside **Copilot Chat in VS Code**. Modules 1 and 3
work entirely against local files. **Module 2 is different: it runs
against each participant's own real Jira project** (through their org's
own MCP server, not a connector we configure) **and their own n8n
instance** (their org's, primarily; a local Docker Compose instance as
Plan B if org n8n isn't reachable — no signup, no trial clock either way).
There is no shared training sandbox for this module — `sample-data/*.csv`
under `module-2-mcp-n8n/` is fallback data for anyone without a suitable
real project, not something pre-seeded for the room. See
`00-setup/setup-guide.md` for exactly what each participant needs, and
each module's `lab-guide.md` for the step-by-step.
