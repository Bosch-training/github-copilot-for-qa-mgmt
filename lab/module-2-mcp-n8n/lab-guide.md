# Lab Guide — Module 2: Live Jira Data & Automated Reporting (MCP + n8n)

**Duration:** ~90 minutes hands-on (Part A ~40 min, Part B ~50 min)
**Tools:** VS Code + Copilot Chat + your org's MCP server, your org's n8n
instance (or the Docker fallback)
**Prerequisite:** `00-setup/setup-guide.md` completed — MCP access to your
own Jira project confirmed, n8n reachable (org instance or, on Plan B,
`docker compose up -d` from `docker/` with http://localhost:5678 reachable),
SMTP credentials for the training mailbox in hand.

> Everything in this module runs against **your own real Jira project and
> your own n8n instance** — there is no shared training sandbox. That's a
> deliberate change from the sample-data approach in Modules 1 and 3: this
> is the first time nothing is a local file, and it's real data, not
> synthetic. Two consequences worth saying out loud before you start: (1)
> whatever you query and summarize is genuinely visible to Copilot/the AI
> provider — pick a low-sensitivity project if you have a choice, per
> `00-setup/setup-guide.md` §3; (2) `sample-data/*.csv` in this folder is
> **reference/fallback only** now — useful if you don't have a suitable
> real project, or want to see the intended shape of the data — not
> something a facilitator pre-seeds for you.

---

## Part A — Live Jira via MCP + Copilot-assisted JQL (40 min)

### A1. Confirm the connection (5 min)

In Copilot Chat:
> "Using the Jira MCP connector, list the open issues in [YOUR PROJECT]."

You should get real issues back from your actual Jira project, not a
generic answer. If not, stop and flag the facilitator — don't burn the
rest of the module debugging MCP.

### A2. Copilot-assisted JQL (15 min)

Ask Copilot to **write and explain** JQL for you rather than writing it
yourself — this is the skill being taught, not raw JQL syntax:

> "Write a JQL query for issues in [YOUR PROJECT] that are type = Bug,
> status is not Done, and priority is Critical or High. Explain the query,
> then run it via MCP."

> "Now write JQL for everything in the current sprint with status = Blocked,
> and tell me which ones have been blocked longest based on their last
> status-change date."

Try refining a query that returns too much or too little — this is the same
iteration muscle from Module 1, now against live structured data.

### A3. Synthesize a coverage-velocity / defect-density view (20 min)

Using MCP-sourced sprint, backlog, and defect data together, prompt Copilot
to produce a single synthesized view:

> "Pull the current sprint's issues, the backlog, and open defects via MCP.
> Produce a short report with: (1) sprint velocity — points done vs.
> committed, (2) defect density — open defects per 10 story points delivered
> this sprint, (3) backlog health — % of backlog items in 'Ready' status,
> (4) one flagged risk if defect density looks high relative to velocity."

Keep this output open — you carry it into Part B unchanged.

---

## Part B — Wire it into a scheduled n8n workflow (50 min)

### B1. Import the starter workflow (10 min)

1. Confirm you can reach n8n: your org's instance URL, or on Plan B
   `docker compose ps` from `docker/` should show it up with
   http://localhost:5678 reachable. If not, see `docker/README.md` (Plan B)
   or your org's n8n docs before going further.
2. In the n8n UI, **Import from File** → `n8n-workflow-starter.json`.
3. You'll see six nodes: **Schedule Trigger → Jira (Get Issues) → Format
   Issues For Prompt (Code) → AI Summary**, with a separate **OpenAI Chat
   Model** node feeding into AI Summary's Model input (that's a different
   kind of connection, not a normal data link — see B2), then **AI
   Summary → Send Email**. Every credential slot is a placeholder — nothing
   runs until you fill them in.
4. Create/attach a Jira credential pointed at **your own Jira project**
   (same one you queried in Part A), your AI provider credential on the
   **OpenAI Chat Model** node, and the training mailbox's SMTP credential
   on **Send Email** — see `00-setup/setup-guide.md`. Update the Jira
   node's JQL too — it ships with a placeholder project/sprint, not yours.
   On an org-shared n8n instance, credentials you create are still scoped
   to workflows you own — don't reuse someone else's saved credential.

### B2. Walk the nodes (10 min)

For each node, open it and read what it does before changing anything:

- **Schedule Trigger** — currently set to run every 15 minutes for the lab
  (production cadence would be daily/weekly — discuss what's appropriate for
  a status report).
- **Jira node** — pulls issues from your project via JQL, same concept as
  Part A but running headless, not through chat.
- **Format Issues For Prompt (Code node)** — reduces the raw Jira issue
  array to a compact text block so the AI prompt stays small. Worth reading
  once; you won't need to edit it.
- **OpenAI Chat Model** — the actual model connection (API key, model
  name). This connects into AI Summary via the thin dashed line at the
  bottom of the canvas, not the normal left-to-right arrow — n8n draws AI
  sub-components that way to distinguish "which model powers this step"
  from "what data flows through it."
- **AI Summary (Basic LLM Chain)** — takes the Code node's output as its
  prompt and the OpenAI Chat Model as its model, produces a natural-
  language summary. This is the automated equivalent of your A3 prompt.
- **Send Email node** — sends the summary to a recipient you configure, from
  the training mailbox.

### B3. Wire in your Part A analysis (20 min)

The starter workflow's AI Summary node has a placeholder prompt in its
**Prompt (User Message)** field. Replace it with the refined prompt
structure from A3 (adapt for the fact there's no back-and-forth here — the
prompt has to work in one shot):

> Edit the AI Summary node's Prompt (User Message) field to request the
> same four sections you got in A3: velocity, defect density, backlog
> health, risk flag.

Before wiring Send Email, click **Execute step** on AI Summary alone and
check the output panel for the field name holding the generated text
(normally `text`) — the Send Email node's message field already expects
that, but confirm rather than assume: node output shapes are exactly the
kind of thing worth a quick manual check before you wire the next step,
same habit as reading a JQL query before trusting it in Part A.

### B4. Test run (10 min)

1. Set the recipient email to your own address.
2. Click **Execute Workflow** manually (don't wait for the schedule).
3. Confirm the email arrives with the four-section summary.
4. Re-enable the schedule trigger only if instructed by the facilitator —
   this is querying your **real** Jira project and, if the recipient field
   isn't still set to your own address, could email real people every 15
   minutes. **Deactivate the workflow before moving on.**

---

## Wrap-up questions (facilitator-led)

- What's different about reviewing an AI output in chat (Part A) vs. one
  that lands in your inbox unattended (Part B)? What review step would you
  add before this goes to a real stakeholder distribution list?
- What cadence would you actually want this on, and why?
