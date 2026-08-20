# Team Meeting Notes — Sprint 14 (synthetic sample data)

## Daily standup — Day 3

- S. Iyer: Camera-radar sensor fusion calibration for FCW (QAM-204) is
  taking longer than estimated — the radar vendor's driver SDK docs are out
  of date. Might slip a day.
- A. Rao: HIL bench test for blind-spot detection (QAM-205) is still flaky
  — intermittent signal noise from the bench rig, suspect the test bench's
  wiring harness, not the perception stack. Blocked on infra team ticket
  INF-88.
- R. Nair: AEB TTC braking threshold tuning (QAM-201) done and merged.
  Starting on HMI accessibility audit (QAM-209) next.
- K. Menon: Heads up — I'm on planned leave Thursday–Friday this week.
  Anything urgent, route to R. Nair.

## Daily standup — Day 5

- A. Rao: Closed-course track test session for ACC (QAM-210) still
  blocked — track booking team says no slot available until next week.
  Escalating to PM.
- A. Rao: Found a critical defect during triage — AEB fires a false-positive
  brake event on an overhead highway gantry sign at speed (QAM-207).
  Reproduced consistently on the test track log replay. Raised as Critical.
- S. Iyer: Sensor fusion calibration (QAM-204) — resolved the radar SDK
  issue, back on track, should complete by sprint end.
- Team: agreed to defer the traffic-sign recognition (TSR) regression suite
  (QAM-208) to next sprint given the two blockers above are eating capacity.

## Sprint review notes

- Demo'd: AEB TTC braking threshold tuning, ACC target-vehicle handoff.
- Stakeholder feedback: the AEB false-positive fix needs functional-safety
  (ASIL) sign-off before it can go to production — new action item, not
  currently tracked in Jira.
- Velocity this sprint: 10 of 46 committed points completed so far (18 in
  progress, 5 blocked, 13 not started) — two blockers ate ~2.5 days of team
  capacity.

## Retrospective notes

**What went well:**
- AEB TTC tuning and ACC handoff logic shipped cleanly, no post-merge
  defects found.
- Triage process caught the AEB false-positive defect before it reached
  track validation.

**What didn't go well:**
- Two separate blockers (flaky HIL bench, track test capacity) both trace
  back to test infrastructure — this is the second sprint in a row this has
  happened.
- Functional-safety (ASIL) sign-off for perception-stack changes isn't in
  our Definition of Done — caused late-sprint surprise.

**Action items:**
- Automate CAN bus log capture in CI to catch flaky-bench issues earlier
  (QAM-211, already in progress).
- Add "ASIL/functional-safety sign-off for perception changes" to
  Definition of Done — owner: R. Nair, due next sprint planning.
- Escalate track test capacity to infra team lead — owner: A. Rao.
