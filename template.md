# [Entry Title]

## Entry Metadata

| Field | Value |
|---|---|
| Title | [Entry title] |
| Type | Project Retrospective / Technical Learning Note / Architecture Reflection / Decision Review / Certification Note / Troubleshooting Note |
| Subject | [Project, topic, certification, or technology area] |
| Date | [YYYY-MM-DD] |
| Status | Draft / In Progress / Complete / Updated |
| Phase | Optional; use for project entries when applicable |
| Sprint | Optional; use for project entries when applicable |
| Issue | Optional; use for project entries when applicable |
| Tags | [comma-separated tags] |
| Related Repo | Optional |
| Related Work | Optional PRs, issues, commits, courses, docs, or references |

## Summary

Summarize the entry in two to four concise paragraphs. Explain what the entry covers and why it matters.

## Context

Explain the situation that prompted this work or learning.

For project entries, include the phase, sprint, problem, constraints, and intended outcome.

For topic entries, include the course, technology, concept, or practical problem being studied.

## Key Ideas / Decisions

Capture the most important concepts, decisions, or takeaways.

Use short subsections when needed.

## Trade-offs

Explain meaningful alternatives, costs, benefits, risks, or constraints.

Delete this section if it does not add value.

## What Changed / What I Learned

For project entries, summarize what changed in the project or workflow.

For topic entries, summarize the reusable knowledge, mental model, or technical skill gained.

## What Was Deferred / Open Questions

List what was intentionally left out, postponed, or still unclear.

Delete this section if it does not add value.

## Lessons Learned

Summarize the durable lessons that should inform future work.

## Current Relevance

Explain how the entry supports portfolio signal, job readiness, certification progress, project execution, or future technical decisions.

## Next Steps

List practical follow-up actions.

## Usage Notes

Use this single template for all journal entries.

### Audience and editorial standard

Write each entry as both a durable learning/history artifact and a public professional reflection.

A broad reader should be able to skim an entry and understand:
- what was accomplished or learned;
- why it mattered;
- what judgment, ownership, or growth the work demonstrates;
- why the experience remains relevant.

A technical reader should be able to continue reading and find enough concrete engineering detail to understand and evaluate the claims.

Prefer:
- plain English before jargon;
- outcome and insight before implementation detail;
- selective personal reflection where it shows changed judgment, uncertainty, or growth;
- the few decisions and trade-offs that materially shaped the work;
- links to repositories, issues, PRs, or technical docs for exhaustive detail;
- short sections and minimal repetition so the entry remains skimmable.

Avoid turning an entry into:
- a changelog;
- an architecture specification;
- a tutorial;
- a transcript of exploratory discussion;
- a diary of every difficulty or decision.

Do not expose private, proprietary, or unnecessary historical implementation detail merely to make the reflection more complete. The goal is a useful, credible record of real work and growth, not exhaustive disclosure.

Organize entries by subject:

- project-specific entries go under `projects/<project>/`;
- reusable technical or certification notes go under `topics/<topic>/`.

For project entry filenames, prefer:

```text
phase-<n>-sprint-<n>-<topic>.md
```

Use phase and sprint together when both are known and useful for navigation. Keep issue numbers in metadata unless the entry is specifically an issue-level postmortem.

For topic entry filenames, prefer concise subject names such as:

```text
aws/ec2-foundations.md
architecture/clean-architecture-boundaries.md
```

Do not use date-prefixed filenames by default. Dates belong in metadata unless the entry is explicitly a chronological log.
