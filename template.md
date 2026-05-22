# [Entry Title]

## Entry Metadata

| Field | Value |
|---|---|
| Title | [Entry title] |
| Type | Project Retrospective / Technical Learning Note / Architecture Reflection / Decision Review / Certification Note / Troubleshooting Note |
| Subject | [Project, topic, or technology area] |
| Date | [YYYY-MM-DD] |
| Status | Draft / In Progress / Complete / Updated |
| Phase | Optional; use for project entries when applicable |
| Sprint | Optional; use for project entries when applicable |
| Issue | Optional; use for project entries when applicable |
| Tags | [comma-separated tags] |
| Related Repo | Optional |
| Related Work | Optional PRs, issues, commits, courses, docs, or references |

## Summary

State the entry's main point in a few concise paragraphs.

## Context

Explain what prompted the work or learning. Include the relevant project, certification path, technical problem, or decision context.

## Key Ideas / Decisions

Capture the most important concepts, design choices, implementation decisions, or learning takeaways.

Use subsections when helpful.

## Trade-offs

Explain the meaningful alternatives, costs, risks, benefits, or constraints.

## What Changed / What I Learned

For project entries, summarize what changed.

For topic entries, summarize the reusable knowledge or mental model gained.

## What Was Deferred / Open Questions

List what was intentionally left out, postponed, or still unclear.

## Lessons Learned

Summarize the durable lessons that should inform future work.

## Current Relevance

Explain how this entry supports current goals, portfolio signal, job readiness, certification progress, or future implementation work.

## Next Steps

List practical follow-up actions.

## Usage Notes

Use this single template for all journal entries.

Organize entries by subject:

- project-specific entries go under `projects/<project>/`;
- reusable technical or certification notes go under `topics/<topic>/`.

For project entry filenames, prefer:

```text
phase-<n>-sprint-<n>-<topic>.md
```

Use only the most specific stable scope that applies. If sprint or issue does not apply, omit that field from the filename and metadata.

Do not use date-prefixed filenames by default. Dates belong in metadata unless the entry is explicitly a chronological log.
