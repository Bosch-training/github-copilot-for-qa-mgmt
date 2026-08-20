# Weak Prompt Example (for Exercise 4)

## The weak first attempt

> "Give me a status update."

## Run it

Run this exact prompt against `sprint-board-export.csv` in Copilot Chat and
look at what comes back.

## Why it underperforms

- **No data source named** — Copilot may not pull from the CSV at all, or
  may guess/hallucinate generic content.
- **No audience** — tone and detail level are a coin flip (too technical for
  an exec, too vague for a peer PM).
- **No structure requested** — output format will vary run to run, which
  breaks reuse as a template.
- **No guardrail** — nothing stops Copilot from inventing a number or status
  that isn't actually in the source file.
- **No definition of "done"** — doesn't say what sections a status update
  must contain, so coverage is inconsistent.

## Your task

Rewrite this into a reusable template with placeholders, e.g.:

```
Using #{data_source}, write a status report for {audience}.
Include: {required_sections}.
Tone: {tone}.
Constraint: only use information present in {data_source} — if something
isn't in the data, say "not available" rather than inferring it.
```

Fill in the placeholders for a Test Manager reporting to a program
stakeholder, then compare your version to
`../../prompt-template-library.md`.
