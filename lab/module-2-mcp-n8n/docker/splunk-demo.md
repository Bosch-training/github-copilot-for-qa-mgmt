# Splunk Demo (client-facing, not a lab exercise)

The client uses Splunk. This shows the same AI-assisted analysis pipeline
from Module 2 feeding into it — not just email, a second delivery channel
into whatever observability/SIEM platform the client already runs. No
exercise, no answer key — this is presenter material for a live demo.

**The idea in one line:** n8n's "AI Summary" node already produces the
sprint status text for Send Email in Module 2's workflow. This adds one
more branch off that same node — an HTTP Request to Splunk's HTTP Event
Collector (HEC) — so the identical AI output also lands in Splunk,
searchable and dashboard-able, seconds after it's generated.

## 1. Start Splunk

```bash
cd lab/module-2-mcp-n8n/docker
# .env already has SPLUNK_PASSWORD if you set up n8n from this same folder;
# if not: cp .env.example .env and fill in both N8N_ENCRYPTION_KEY and
# SPLUNK_PASSWORD (8+ chars, 1 uppercase, 1 digit — Splunk enforces this)
docker compose up -d splunk
```

First boot runs Splunk's internal provisioning (Ansible) and takes a few
minutes — don't judge it by n8n's ~10-second boot. Watch for it:

```bash
docker compose logs -f splunk   # look for "Ansible playbook complete" then Ctrl-C
```

Open **http://localhost:8001** (plain HTTP — Splunk Web doesn't enable SSL
by default, so no cert warning here) and log in with `admin` / your
`SPLUNK_PASSWORD`. It's 8001, not Splunk's usual 8000 — that port is
commonly already taken by other local projects, so this compose file maps
it to 8001 on the host instead (Splunk itself is still using 8000
internally, nothing to reconfigure inside Splunk).

This ships as the full 60-day Splunk Enterprise Trial by default (that's
what `--accept-license` alone gives you, no extra config) — plenty for a
demo. Splunk auto-converts an unused trial to the perpetual single-instance
Free license (500MB/day) after 60 days on its own; nothing to do here.

## 2. Create a HEC token

Settings (top right) → **Data Inputs** → **HTTP Event Collector** → **New
Token**:

- Name: `qam-n8n-demo`
- Leave source type as default (or pick `_json`)
- Select an index — default `main` is fine for a demo
- **Review** → **Submit** → note the token (a UUID) it generates. If HEC
  itself shows as disabled, there's a toggle at the top of the HTTP Event
  Collector page — enable it first.

## 3. Add the Splunk branch to the n8n workflow

In the same workflow from Module 2's Part B, add an **HTTP Request** node
connected off **AI Summary**'s output (parallel to Send Email — both read
from the same upstream node, this doesn't touch the existing branch):

- Method: `POST`
- URL: `https://splunk:8088/services/collector/event` — **https, not
  http** (HEC has SSL on by default, confirmed against a running
  instance). If n8n and Splunk are both containers on the same Docker
  network (they are, with this compose file), use the service name
  `splunk` rather than `localhost` — from *inside* a container, `localhost`
  means that container, not the host.
- Since it's a self-signed cert, enable **"Ignore SSL Issues"** in the
  node's options (or equivalent — n8n's HTTP Request node has a toggle for
  this; without it the request fails cert validation)
- Header: `Authorization: Splunk <your HEC token>`
- Body (JSON):
  ```json
  {
    "sourcetype": "qam:sprint-summary",
    "event": {
      "summary": "={{ $json.text }}",
      "sprint": "Sprint 15"
    }
  }
  ```

## 4. What to show live

Run the workflow once (B4's manual test), then in Splunk's **Search &
Reporting** app:

```spl
index=main sourcetype="qam:sprint-summary"
| spath
| table _time, summary, sprint
```

The `| spath` matters — without it, `summary`/`sprint` aren't parsed out
of the raw JSON as searchable fields (verified against a real event; don't
drop it or the table comes back empty). Also note: no `event.` prefix on
the field names — HEC unwraps the `"event"` key from your payload straight
into the indexed text, so the field is just `summary`, not `event.summary`.

That alone is the demo: an AI-generated summary from a live Jira query,
searchable in Splunk within seconds of being produced. If there's time for
a second search, this is the honest "not authoritative yet" point made
visual — same data, human-readable framing of the automation's own
delivery record:

```spl
index=main sourcetype="qam:sprint-summary"
| spath
| stats count by sourcetype
| eval note="each event here still needs the human review step from B4 — this just proves delivery, not correctness"
```

## Cleanup

`docker compose stop splunk` when done — leave `down -v` only for when you
want to wipe the demo index/HEC token and start clean next time.
