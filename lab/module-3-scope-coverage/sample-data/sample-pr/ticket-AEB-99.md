# AEB-99 — Fix false AEB activation from radar multipath reflection

**Type:** Bug / Safety
**Priority:** Critical
**Reporter:** A. Rao (QA)
**Linked defect:** QAM-315 (same underlying issue road-tested and first
flagged during Sprint 14 triage — see Module 1's meeting notes / QAM-207)

## Description

AEB's object-fusion logic trusts raw radar range and closing-velocity
values without validating them against a physically plausible envelope. A
closed-track investigation found that a radar multipath reflection — e.g.
off an overhead highway gantry sign — can report a bogus close-range,
high-closing-velocity "object" that doesn't correspond to anything
physically present ahead. The fusion layer currently has no way to
distinguish this from a real threat, so AEB fires an unwarranted hard
brake at highway speed.

## Acceptance Criteria

1. AEB object-fusion logic must validate a radar return against a
   camera-confirmed range-rate envelope before trusting it for a braking
   decision (requirement AEB-99).
2. A radar return that fails the plausibility check must fall back to
   camera-only detection for that frame — never suppress AEB entirely.
   Losing confidence in one sensor input must never mean losing the safety
   function altogether.
3. Existing AEB activation thresholds (the TTC trigger point) are
   unchanged — this ticket is about *validating the sensor input*, not
   changing *when* AEB fires once that input is trusted.
4. No changes to any other perception or brake-actuation logic. This is
   scoped narrowly to radar-return validation in the fusion layer.

## Out of scope (explicitly)

- Brake-actuator ramp-time / control-loop tuning
- Any change to the CAN message schema published to other ECUs
- Removing or deprecating any existing sensor input path
