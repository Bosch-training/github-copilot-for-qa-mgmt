# Running n8n locally via Docker (Module 2)

Each participant runs their **own** n8n container on their own laptop —
this replaces the n8n Cloud trial referenced as the default in the course
outline. Rationale: no signup/trial-expiry dependency, no shared tenant for
15 people's workflow runs to collide on, and every participant gets an
identical, disposable environment.

## Prerequisites

- Docker Desktop (Windows/Mac) or Docker Engine + Compose plugin (Linux)
  installed and working — add this to the pre-work checklist
  (`00-setup/setup-guide.md`) alongside local admin access, which is already
  a stated hardware requirement.
- Port `5678` free on localhost.

## Start it

```bash
cd lab/module-2-mcp-n8n/docker
cp .env.example .env
# edit .env: set a real N8N_ENCRYPTION_KEY
#   generate one with: openssl rand -hex 32
docker compose up -d
```

Open http://localhost:5678 — first load shows n8n's own **"Set up owner
account"** screen (email + password you choose). That account is local to
your container only, stored in its own database, not shared with anyone
else. This *is* your login going forward; there's nothing else to configure
for auth.

Whatever you set, write it down somewhere gitignored — e.g. copy
`n8n-credentials.local.md` (already gitignored, see repo-root
`.gitignore`) and fill in your own values. Don't commit real credentials,
even local-only ones.

## During the lab

- Import `../n8n-workflow-starter.json` as instructed in
  `../lab-guide.md` step B1.
- All credentials you create (Jira, AI provider, SMTP) live inside your own
  container's encrypted local database — nothing is shared with other
  participants' instances.
- The container reaches the internet the same as your laptop does (your
  Jira Cloud site, your AI provider's API, SMTP) — no inbound access is
  needed since this lab only uses a Schedule Trigger, not a webhook.

## Stop / reset

```bash
docker compose down        # stop, keep data (workflows/credentials persist)
docker compose down -v     # stop AND wipe all data — use between cohorts,
                            # or if you want to start Module 2 over clean
```

## Troubleshooting

- **Port 5678 already in use** — another process (maybe a previous n8n
  container) is bound to it. `docker compose down` any prior instance, or
  change the port mapping in `docker-compose.yml`.
- **Container won't start / restarts in a loop** — check `.env` actually has
  a value for `N8N_ENCRYPTION_KEY`; the compose file intentionally fails
  fast (`:?`) if it's missing rather than booting with a blank one (a blank
  key still "works" but makes your stored credentials unrecoverable if the
  volume is ever recreated with a different key).
- **Forgot your owner-account password** — there's no admin reset in the
  Community Edition UI for a lost password. Fastest fix for a lab: wipe and
  start over with `docker compose down -v` then `docker compose up -d`, and
  go through "Set up owner account" again.
- **Corporate laptop won't run Docker** — this is the fallback case for the
  n8n Cloud trial route in `00-setup/setup-guide.md`. Confirm during
  pre-work, not on the day.
