# Answer Key — Coverage-Gap Check (Exercise 2)

## Section 1 — Requirements with NO test case in this build

| Requirement | Title | Priority | Note |
|---|---|---|---|
| AEB-114 | TTC recalculation on changing deceleration | Critical | No test exists at all — and this maps to an already-open **Critical** defect (QAM-306) from the Module 2 seed data. A critical, known-broken flow with zero regression coverage is the single most important finding in this exercise. |
| AEB-121 | Manual-override event logging | High | No test exists — also maps to an open defect (QAM-311, override logged twice). Same pattern: known issue, no automated coverage. |
| AEB-125 | Warning accessibility redundancy | Medium | No test exists. No corresponding defect on file either — this one may simply never have been tested, as distinct from a known-and-untested regression. |

## Section 2 — Requirements with a test case that failed

| Requirement | Title | Priority | Log cross-reference |
|---|---|---|---|
| AEB-105 | Pre-collision warning latency | Medium | Log shows `warning-output capture rig queue depth=142, above normal threshold` right before the failure — **likely a test-rig/instrumentation issue** (the capture rig backing up), not necessarily a functional defect. Lower confidence this is a real product bug; worth re-running on a clean rig before filing. |
| AEB-108 | Graceful degradation under sensor overload | Critical | Log shows `sensor_fusion_buffer exhausted` — could be a genuine capacity/degraded-mode gap (the requirement is specifically about graceful degradation under load) **or** a test-harness sizing issue feeding synthetic frames faster than a real radar could. Medium confidence — this is exactly the kind of finding that needs a human to check test-harness realism before treating it as a product defect. |
| AEB-110 | Re-arm after full stop | High | No environment signal in the log — AEB simply didn't re-arm on schedule. **Higher confidence this is a genuine functional defect**, not a test-environment artifact. |

## Key teaching points

1. **"No test" and "test failed" are different problems** — a participant
   or Copilot output that lumps AEB-114 in with AEB-105 as equally "gaps"
   is missing the distinction the exercise is built around. Zero coverage
   on a *known critical defect* (AEB-114) is arguably the most urgent
   finding in the whole report, and it wouldn't show up at all in a report
   that only looks at pass/fail results.
2. **Log cross-referencing should change confidence, not just add color.**
   AEB-105's capture-rig queue warning is a reason to *suspect* test-
   instrumentation noise before escalating; AEB-110 has no such signal, so
   it should be treated as a more credible functional finding.
3. **This is advisory.** The expected output explicitly flags "needs human
   confirmation" — a participant whose Copilot output states conclusions
   as fact without that caveat is a good discussion point for the
   governance segment that follows. In this domain specifically, treating
   an AI-generated coverage report as authoritative rather than advisory
   for a safety-relevant feature like AEB is the exact failure mode the
   governance discussion should land on.
