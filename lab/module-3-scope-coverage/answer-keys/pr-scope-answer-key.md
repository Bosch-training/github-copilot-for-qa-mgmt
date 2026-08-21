# Answer Key — PR Scope Check (Exercise 1)

## Expected findings

| # | Change | In/Out of scope | Why |
|---|---|---|---|
| 1 | `sensorFusion.c` — validate radar return against camera-confirmed range-rate, fall back to camera-only on failure | **In scope** | Directly matches AEB-99's acceptance criteria 1 & 2 |
| 2 | `brakeActuator.c` — brake ramp time shortened 400ms → 180ms | **Out of scope** | Ticket explicitly excludes "brake-actuator ramp-time / control-loop tuning." A safety-critical actuation change bundled into a critical-priority perception fix — should be its own ticket/PR with its own validation (a faster ramp affects ride comfort and possibly other safety cases; needs separate impact analysis, not a drive-by change) |
| 3 | `aebStatusFrame.c` — added `ttc_estimate_ms` to the AEB status CAN frame | **Out of scope** | Ticket explicitly excludes "any change to the CAN message schema published to other ECUs." This changes a message contract other ECUs (here, infotainment) depend on — a silent CAN schema change is the highest-risk item in this PR even though it sounds benign ("infotainment team asked for it") |
| 4 | `legacyRadarCanMap.c` — deleted the legacy radar hardware's CAN ID mapping entirely | **Out of scope** | Ticket explicitly excludes "removing or deprecating any existing sensor input path." Deleting a sensor input mapping based on the author's assumption ("nothing should be using it anymore") without verification is exactly the kind of change that needs its own deprecation-tracking ticket — not a hitchhiker on a critical safety fix |

## Key teaching point

Three of four changes are individually defensible-sounding ("the safety
team asked for it," "the infotainment team asked for it," "it's already
deprecated") — that's what makes this a good scope-check example. A weak
prompt or a rushed reviewer accepts the PR description's framing at face
value. The exercise is testing whether Copilot (and the participant) holds
the line against the ticket's explicit "out of scope" list rather than
against the PR author's own narrative — and in an automotive/ADAS context,
"it sounds like a small tweak" carries real safety stakes, not just
convenience risk.

## What a good Copilot output looks like

- Enumerates all four changes distinctly (not just "mostly looks fine")
- Cites the ticket's explicit out-of-scope bullets, not just general judgment
- Flags the CAN schema change as the highest operational risk (silent
  breakage of another ECU's consumer, potentially field-discoverable only
  after the fact), not just "unrelated"
- States this is advisory and a human reviewer — ideally with functional-
  safety sign-off given the domain — should confirm before requesting
  changes on the PR
