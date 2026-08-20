# Pre-Work & Environment Setup — GenAI for Test Management

Send this to participants **3–5 business days before Day 1**. Nothing in the
workshop works without this being done in advance — there is no setup time
budgeted inside the 8-hour agenda.

## 1. Hardware / access

- [ ] Desktop or laptop, 16 GB RAM minimum
- [ ] Open internet connection (not behind a proxy that blocks GitHub/your
      org's Jira, MCP server, and n8n)
- [ ] Local admin rights (to install the VS Code extension)
- [ ] **Docker Desktop or Docker Engine + Compose plugin — only if you're on
      the n8n fallback path (see §4).** Not needed if your org's n8n
      instance is reachable.

## 2. GitHub Copilot

- [ ] VS Code installed (latest stable) — or confirm you'll use Copilot Chat in
      the browser instead
- [ ] GitHub Copilot extension installed and **signed in with your licensed,
      provisioned seat** (verify with your GitHub org admin beforehand —
      un-provisioned seats is the #1 day-of blocker)
- [ ] Confirm Copilot Chat opens and responds to a test prompt: *"Say hello and
      confirm you're ready."*

## 3. Your own Jira + your org's MCP server

There is no shared training sandbox for this module. You'll query **your
own organization's Jira** through **your organization's own MCP server**
(not a personal connector pointed at Atlassian's public remote MCP) —
confirm with your IT/platform team that this is already provisioned before
you try to configure anything yourself.

- [ ] Confirm you have a **real Jira Cloud project** you can use for this
      exercise — ideally one with an active or recent sprint, so JQL
      queries in Part A return something meaningful. Pick a project with
      **low-sensitivity data**: this exercise sends real ticket content
      (summaries, comments, whatever's in scope) to Copilot, so this is
      also the moment to check your org's AI/data-handling policy on what's
      OK to pass to an LLM — this is one of the course's own learning
      objectives (compliance/risk with GenAI + project data), not just
      setup admin.
- [ ] Get the MCP server connection details from your IT/platform team
      (endpoint, auth) and configure it in VS Code — the exact steps depend
      on how your org has it set up; ask them for their internal how-to
      rather than following Atlassian's generic public-MCP docs.
- [ ] Confirm it works: ask Copilot Chat *"List the open issues in
      [YOUR PROJECT] assigned to me"* and get a real answer back from your
      real Jira.

### Also get a personal Jira API token (needed later, for n8n — Part B)

The MCP server above is only for Copilot Chat in VS Code. n8n's workflow in
Part B uses a **different, direct connection** to Jira and needs its own
credential — get this now so it's not a Part B surprise:

- [ ] Log into your Atlassian account →
      https://id.atlassian.com/manage-profile/security/api-tokens →
      **Create API token** → label it (e.g. `n8n-workshop`) → **set an
      expiration date** (Atlassian requires one, 1–365 days — pick
      something that outlasts the session, e.g. 30 days) → **Create** →
      copy it immediately, it's shown once and can't be recovered later
- [ ] Note your Jira **domain** (`https://<yoursite>.atlassian.net`) and
      the **email** tied to that Atlassian account alongside the token —
      n8n's Jira credential asks for exactly these three fields
- [ ] If prompted to verify your identity via a one-time passcode before
      the token generates, that's expected for password/SSO accounts —
      not an error

## 4. n8n

### Primary path — your org's own n8n instance

- [ ] Confirm you have login access to your organization's n8n instance
      (get the URL and your credentials from IT/platform team if you don't
      already use it day-to-day).
- [ ] Confirm you can create/import a workflow and add credentials there —
      you'll need permission to add your own Jira, AI-provider, and SMTP
      credentials to a workflow you own, not just view existing ones.
- [ ] `lab-guide.md`'s Part B steps assume you can reach your n8n instance
      at whatever URL your org uses — swap out any `localhost:5678`
      reference for that URL as you go.

### Plan B — self-hosted via Docker, only if org n8n isn't reachable

If your org's n8n instance is unavailable, unreachable from your network,
or you're doing this module standalone without org access, fall back to a
local, self-hosted instance:

- [ ] Docker Desktop or Docker Engine + Compose plugin installed
      (`docker compose version` to confirm)
- [ ] From `lab/module-2-mcp-n8n/docker/`, run:
      `cp .env.example .env` then edit `.env` — generate an
      `N8N_ENCRYPTION_KEY` (`openssl rand -hex 32`) and set it there
- [ ] `docker compose up -d`, then open http://localhost:5678 — n8n shows
      its own "Set up owner account" screen on first load (pick an
      email/password, local to your container only). That's your login
      going forward.
- [ ] Full instructions and troubleshooting:
      `lab/module-2-mcp-n8n/docker/README.md`

**Either path:** do not connect n8n to a personal or production email
account for the Send Email node — see §5.

## 5. Dedicated training email account

- [ ] Create a **dedicated training mailbox** for SMTP credentials used by
      the n8n workflow in Module 2. This account sends the automated report
      emails during the exercise — never use a personal inbox.
- [ ] **If it's Google: it must be a personal-tier Google account, not a
      Google Workspace / company-domain one.** As of May 1, 2025, Google
      Workspace accounts no longer support username+password (or App
      Password) SMTP at all — Google hard-blocked it platform-wide, it's
      not a config nuance. n8n's SMTP credential is username/password
      only, so a Workspace mailbox will fail outright here, every time,
      regardless of settings. **Have IT create a plain personal Google
      account** (`@gmail.com`, not your company's Workspace domain) set
      aside for this training, or use a non-Google provider whose SMTP
      still supports basic auth (many corporate mail relays and
      transactional-email services still do — check with whoever
      administers it).
- [ ] Confirm SMTP host/port/username/password (or app password) for that
      mailbox before Day 1

**If it's a personal-tier Gmail account**, the setup is:

1. Enable 2-Step Verification on it — https://myaccount.google.com/security
   (required before an App Password can exist)
2. Generate an App Password — https://myaccount.google.com/apppasswords →
   app "Mail", device "Other (custom name)" → copy the 16-character result
3. In n8n's SMTP credential: Host `smtp.gmail.com`, Port `587` with
   SSL/TLS **off** and "Disable STARTTLS" left off too (Google's own
   recommended combination) — or Port `465` with SSL/TLS **on** as the
   alternative — User = the mailbox's full address, Password = the App
   Password, not the account's normal password
4. Set the Send Email node's **From Email** to that same address — Gmail
   enforces the match
5. Test-send to yourself and check spam before trusting it's working

If it's a different provider, get the equivalent Host/Port/User/Password
from whoever administers that mailbox — the n8n-side fields are the same
regardless of provider, but confirm they haven't deprecated basic-auth SMTP
too (Microsoft 365/Exchange Online did the same thing back in 2022 — this
isn't just a Google policy).

## 6. Lab files

- [ ] Clone/download this repo's `lab/` folder, or receive the zipped module
      folders from the facilitator
- [ ] No need to open anything yet — each module's `lab-guide.md` tells you
      which files to open, when

## Quick self-check (5 minutes, do this the night before)

1. Open VS Code → Copilot Chat responds ✅
2. Ask Copilot Chat a question that requires your org's MCP server (e.g.
   list issues from your real Jira project) → get real data back ✅
3. Log into your org's n8n instance and confirm you can create a workflow
   *(or, on Plan B: `docker compose up -d` in
   `lab/module-2-mcp-n8n/docker/` → http://localhost:5678 loads and you can
   log in)* ✅
4. Confirm you know the SMTP credentials for the training mailbox ✅

If any of these fail, contact the facilitator **before** Day 1 — Module 2 in
particular cannot be completed live-troubleshooting a broken connector.
