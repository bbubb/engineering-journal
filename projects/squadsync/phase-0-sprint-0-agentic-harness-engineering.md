# SquadSync Phase 0 Agentic Harness — Designing an AI-Assisted Workflow

**Project Retrospective** · SquadSync · Phase 0, Sprint 0 · May 2026

<details>
<summary>Metadata & related work</summary>

| Field | Value |
|---|---|
| Status | Complete — design phase |
| Tags | agentic-workflow, context-engineering, codex, human-review, tooling |
| Related Repo | https://github.com/bbubb/squadsync |
| Related Work | PRs #39 and #42; companion Phase 0 foundation and Phase 1 retrospectives |

</details>

## Summary

I wanted a low-cost way to use ChatGPT, GitHub, and Codex CLI to build SquadSync without losing control of scope, testing, or merges. During Phase 0, I designed a step-by-step workflow that gave each tool a clear job and kept the decisions in the repository.

Phase 0 produced instructions and a workflow design, not an autonomous multi-agent system. I still needed real implementation tasks to find out whether the instructions, Git operations, and review handoffs worked.

## Key Outcomes

- Stored decisions, task scope, and review history in GitHub instead of relying on past chat conversations.
- Separated tool-independent engineering rules from Codex- and ChatGPT-specific instructions.
- Distinguished readable workflow documentation from native tool configuration and executable automation.
- Deferred hooks, active subagents, and other automation until real friction justified them.

## Engineering Reflections

### GitHub issues gave each implementation task a clear starting point

An AI coding session can start without knowing why a decision was made or which files it is allowed to change. The design therefore put product scope, architecture, agent instructions, task contracts, and validation expectations in versioned repository artifacts.

GitHub issues were intended to describe bounded changes and acceptance criteria; pull requests would expose the implementation for review. This required extra documentation, but meant a new session could read the task and its constraints without relying on my previous conversations.

I had to decide how much context to provide. Too little encourages guessing; too much hides the actual task in unrelated documentation. Scoped instructions and issue-first execution were the preferred direction, to be validated during implementation.

### I kept architecture and merge decisions under human control

I separated decisions about the product and architecture from the implementation work Codex could perform. I retained responsibility for scope, trade-offs, validation, and merges; AI tools could draft, implement approved slices, and surface questions.

The same project rules could guide different tools, but Codex skills, command permissions, and GitHub operations needed instructions specific to each tool. One important correction was recognizing that a document *describing* a skill is not the same as a tool-native skill the agent can invoke.

That created more instruction files to maintain. I wanted the shared rules to stay short and stable, with tool-specific details added only where needed.

### Writing workflow instructions did not prove they worked

I considered reusable skills, hooks, subagents, and staged planning, but chose to start with one implementation agent working on one scoped issue at a time. I prepared tool-recognized Codex skills and scoped instructions. Hooks, autonomous subagents, MCP integration, and multi-agent orchestration were not running.

I risked spending more time designing the workflow than building SquadSync. I closed Phase 0 with a simpler rule: keep only the instructions needed to start coding, then improve them when actual tasks reveal a problem.

## Significance

I learned that useful AI assistance depends more on clear task scope and human review than on the number of agents or tools. Phase 0 defined that approach; Phase 1's API work showed where the instructions helped and where they needed correction.
