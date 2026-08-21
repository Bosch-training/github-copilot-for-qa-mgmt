---
name: test-summary-pptx
description: 'Classify test execution results (Passed/Failed/Not Executed) for any test suite, link failed tests to defects, verify passed tests ran a clean single iteration, and generate a PPTX summary with a donut chart. Use when asked to classify test results, produce a test execution summary, check defect linkage, verify test iterations, or generate a pptx test report for any domain (ADAS/AEB, web, mobile, API, etc).'
---

# Test Summary PPTX

Turns raw test execution data (report/log) + a requirements list + a defect
tracker export into a defensible, exec-ready PPTX — for any test suite, not
just AEB. Generalized from the AEB regression-suite prompt template.

## When to Use
- "Classify these test results as passed/failed/not executed"
- "Which failed tests have a linked defect?"
- "Summarize this test run into a pptx with a donut chart"
- Any test-execution + defect-tracker export pair, regardless of domain

## Constraints
- Only report a Passed/Failed/Not Executed status backed by the actual
  test results/requirements source. Never infer a result.
- Never invent a defect link. If a failed test has no match in the defect
  source, mark it explicitly as an orphan failure needing a new defect.
- Only mark a passed test's iteration as "Correct" if the execution log
  shows a single clean run; flag anything with a retry.

## Procedure
1. Read the test execution report/log, full requirement list, and defect
   tracker export.
2. Classify every requirement: Passed, Failed, or Not Executed (no
   corresponding test case = Not Executed).
3. For each Failed test: capture the failure reason and cross-reference
   the defect source by linked requirement ID — note the defect ID/status,
   or flag as an orphan failure if none exists.
4. For each Passed test: confirm from the execution log it ran a single
   clean iteration with no retries; flag any retry as needing a closer look.
5. Build the config JSON matching the schema below (see
   [example-config.aeb.json](./assets/example-config.aeb.json) for a full
   worked example).
6. Run [generate_pptx.py](./scripts/generate_pptx.py) with `--config` (and
   optional `--output`) to produce the deck.

```bash
python3 .github/skills/test-summary-pptx/scripts/generate_pptx.py \
  --config path/to/report-config.json \
  --output output/<today's date, YYYY-MM-DD>.pptx
```

## Config Schema
```jsonc
{
  "title": "string",
  "subtitle": "string",
  "report_date": "YYYY-MM-DD",
  "output_path": "output/<date>.pptx",   // used if --output not passed
  "classification": {
    "note": "string",
    "rows": [["Requirement ID", "Test title", "Passed|Failed|Not Executed"], ...]
  },
  "failed_defect_linkage": {
    "note": "string",
    "rows": [["Requirement ID", "Failure reason", "Defect ID or 'None'", "Defect status"], ...],
    "footer": "string"
  },
  "passed_iteration_check": {
    "note": "string",
    "rows": [["Requirement ID", "Test title", "Iterations logged", "Iteration result"], ...],
    "footer": "string"
  },
  "summary": {
    "donut_labels": ["Passed", "Failed", "Not Executed"],
    "donut_values": [n, n, n],
    "bullets": [["bullet text", true_or_false_bold], ...]
  }
}
```

Status-like values (`Passed`, `Failed`, `Not Executed`, `Open`, `Blocked`,
`Correct`, ...) are auto-colored by the script — no color logic needed in
the config.

## Output Format
A `.pptx` saved to `output/<today's date, YYYY-MM-DD>.pptx` at the repo
root with 5 slides: title, classification table, failed-tests defect
linkage table, passed-tests iteration-check table, and a summary slide
with bullets + donut chart. Report back the file path and a short recap
of the counts.
