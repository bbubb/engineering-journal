# [Entry Title]

**[Entry Type]** · [Subject] · [Phase, topic, or focus when useful] · [Month YYYY]

<details>
<summary>Metadata & related work</summary>

| Field | Value |
|---|---|
| Status | Draft / In Progress / Complete / Updated |
| Sprint | Optional; project entries only when useful |
| Issue | Optional; project entries only when useful |
| Tags | [comma-separated tags] |
| Related Repo | Optional |
| Related Work | Optional PRs, issues, commits, courses, docs, or references |

</details>

## Summary

In two to three concise paragraphs, explain what this entry covers, what changed or was learned, and why it matters.

Write this so a broad reader can understand the significance without needing the implementation details.

## At a Glance

Use three to five short bullets for the most important outcomes, insights, or lessons.

Do not repeat the Summary sentence-for-sentence. This section should let a reader decide quickly whether to continue.

## Key Themes / Reflections

Organize the body around the few ideas that mattered most.

Each theme should be self-contained. Keep the relevant context, decision or insight, trade-off, personal reflection, and remaining uncertainty together instead of distributing one idea across separate global sections.

### [Meaningful theme heading]

Explain the idea in plain English first, then add only the technical detail needed to substantiate it.

Include trade-offs, changed judgment, or uncertainty here when they belong to this theme.

### [Another meaningful theme heading]

Repeat only for distinct ideas worth preserving.

Prefer two to four strong themes over a long inventory of implementation details.

## Open Questions / What Remains

Optional.

Use only for meaningful cross-cutting questions, deferred decisions, or unresolved ideas that do not fit naturally inside one theme.

Delete this section when it would add little value.

## Why It Matters

Tie the entry together.

Explain why the work or learning remains useful: technically, professionally, for future decisions, or as evidence of growth. Keep this broader than a repetition of the individual themes.

## What's Next

Optional.

Record the next meaningful direction when the entry naturally leads to one. Avoid listing routine tasks simply because they happen next.

## Usage Notes

Use this single template for all journal entries.

The headings are a framework, not a checklist. Omit, rename, or combine optional sections when that improves clarity or avoids repetition. Do not create content merely to fill every heading.

### Audience and editorial standard

Write each entry as both a durable learning/history artifact and a public professional reflection.

A broad reader should be able to skim the title, Summary, At a Glance, headings, and Why It Matters section and understand:
- what was accomplished or learned;
- why it mattered;
- what judgment, ownership, or growth the work demonstrates;
- why the experience remains relevant.

A technical reader should be able to continue into the themes and find enough concrete engineering detail to understand and evaluate the work.

Prefer:
- plain English before jargon;
- outcome and insight before implementation detail;
- theme-centered storytelling rather than category-centered reporting;
- selective personal reflection where it shows changed judgment, uncertainty, or growth;
- the few decisions and trade-offs that materially shaped the work;
- links to repositories, issues, PRs, or technical docs for exhaustive detail;
- short sections and minimal repetition.

Avoid turning an entry into:
- a changelog;
- an architecture specification;
- a tutorial;
- a transcript of exploratory discussion;
- a diary of every difficulty or decision.

Do not expose private, proprietary, or unnecessary historical implementation detail merely to make the reflection more complete. The goal is a useful, credible record of real work and growth, not exhaustive disclosure.

### Entry-type emphasis

Use the same flow with different emphasis rather than maintaining separate templates:

- **Project Retrospective:** outcomes, consequential decisions, trade-offs, and changed judgment.
- **Technical Learning Note:** concepts, mental models, and practical connections.
- **Architecture Reflection / Decision Review:** decision, alternatives, consequences, and evolving judgment.
- **Certification Note:** concepts reinforced, connections to prior experience, and practical relevance.
- **Troubleshooting Note:** problem, diagnosis, resolution, and prevention.

### Metadata

Keep the visible metadata line compact. Put archival/navigation detail in the collapsed `Metadata & related work` block.

Do not repeat the title inside metadata.

### Organization and filenames

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
