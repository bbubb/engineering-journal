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

In one or two short paragraphs, explain what you built, changed, decided, or learned. State the most important result early, using concrete nouns and verbs. A reader unfamiliar with the project should understand the outcome without reading the implementation details.

## Key Outcomes

Use two to four short bullets naming concrete results, decisions, or lessons. For a certification or learning note, **Key Insights** may fit better.

The Summary explains the story; these bullets provide quick evidence or distinct lessons. Do not restate the Summary or list technologies without explaining what they enabled.

## Engineering Reflections

Organize the body around the few ideas that mattered most. Use specific subheadings that tell a skimming reader what happened or what you learned.

Each theme should be self-contained. Keep the relevant context, decision or insight, trade-off, personal reflection, and remaining uncertainty together instead of distributing one idea across separate global sections.

### [Meaningful theme heading]

Explain the idea in plain technical English first, then add the detail needed to substantiate it. Prefer a concrete example over abstract architecture language. Define an unfamiliar term briefly when necessary.

Include trade-offs, changed judgment, or uncertainty here when they belong to this theme.

### [Another meaningful theme heading]

Repeat only for distinct ideas worth preserving.

Prefer two to four strong themes over a long inventory of implementation details.

## Open Questions

Optional.

Use only for meaningful cross-cutting questions, deferred decisions, or unresolved ideas that do not fit naturally inside one theme.

Delete this section when it would add little value.

## Significance

In a short closing paragraph, explain what changed in your engineering judgment, understanding, or approach, and why you would apply that lesson again. Do not repeat the Summary or sell the work with generic portfolio claims. Omit this section if it adds no distinct insight.

## Next Steps

Optional.

Record the next meaningful direction when the entry naturally leads to one. Avoid listing routine tasks simply because they happen next.

## Usage Notes

Use this single template for all journal entries.

The headings are a framework, not a checklist. Omit or combine optional sections when that improves clarity or avoids repetition. Rename **Key Outcomes** to **Key Insights** for learning notes when appropriate. Do not create content merely to fill every heading.

### Audience and editorial standard

Write each entry as both a durable learning/history artifact and a public professional reflection.

A broad reader should be able to skim the title, Summary, Key Outcomes (or Key Insights), theme headings, and Significance and understand:
- what was accomplished or learned;
- why it mattered;
- what judgment, ownership, or growth the work demonstrates;
- why the experience remains relevant.

A technical reader should be able to continue into the themes and find enough concrete engineering detail to understand and evaluate the work.

Prefer:
- plain technical English before jargon; concrete nouns and verbs before abstractions;
- first-person judgment when it is genuine, without invented emotions, hindsight, or self-promotion;
- outcome and insight before implementation detail;
- theme-centered storytelling rather than category-centered reporting;
- selective personal reflection where it shows changed judgment, uncertainty, or growth;
- the few decisions and trade-offs that materially shaped the work;
- links to repositories, issues, PRs, or technical docs for exhaustive detail;
- short sections and minimal repetition;
- accurate distinctions between work completed at the time, work planned then, and changes made later.

Avoid turning an entry into:
- a changelog;
- an architecture specification;
- a tutorial;
- a transcript of exploratory discussion;
- a diary of every difficulty or decision.

Do not expose private, proprietary, or unnecessary historical implementation detail merely to make the reflection more complete. The goal is a useful, credible record of real work and growth, not exhaustive disclosure.

**Plain-language test:** Replace a sentence like “separate projects constrain dependency direction” with the concrete mechanism: “The Domain project does not reference Infrastructure, so business rules cannot call the database implementation directly.” Prefer the sentence a technically capable reader can understand without already knowing this project.

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

### Titles, organization, and filenames

Keep major project identifiers such as **Phase 0**, **Sprint 0**, and **Phase 2** in project entry titles when relevant. Add a descriptive topic or takeaway rather than replacing those identifiers or repeating a generic phase name. For example: `SquadSync Phase 0 Foundation — Finding a Buildable MVP Boundary`.

Before publishing, check the entry against the relevant repository/ADR/PR evidence. Preserve the vocabulary and decisions used *at the time*; briefly note later renames or reversals when needed to prevent confusion. Make sure the title, Summary, Key Outcomes, and Significance can be understood without the body. Do not fabricate personal reflections or claim planned features as delivered.

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
