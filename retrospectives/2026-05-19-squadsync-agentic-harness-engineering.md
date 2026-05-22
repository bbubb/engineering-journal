# SquadSync Agentic Harness Engineering Retrospective

## Snapshot

| Field | Value |
|---|---|
| Project | SquadSync |
| Entry Type | Harness engineering retrospective |
| Phase | Phase 0 — Agent-Ready Project Foundation |
| Date | 2026-05-19 |
| Status | Complete / ready to test in Phase 1 |
| Related Repository | https://github.com/bbubb/squadsync |

## Summary

During SquadSync Phase 0, the project expanded from a standard MVP setup into an experiment in low-cost, sequential agentic software development.

The central question was whether a solo developer using ChatGPT Plus, GitHub, and Codex CLI could approximate some of the benefits of a more expensive multi-agent harness: role separation, durable context, repeatable task execution, validation gates, and human review control.

The resulting workflow is not a fully autonomous multi-agent system. It is a sequential, repo-driven harness where ChatGPT supports architecture and planning, GitHub stores durable task and decision state, Codex CLI handles scoped implementation, and the human owner remains responsible for architecture, review, and merge decisions.

## Problem and Motivation

The original motivation was practical: build a portfolio-quality MVP quickly while learning modern AI-assisted engineering.

The deeper question became:

```text
Can a low-cost, sequential agentic workflow be structured professionally enough to support real implementation work?
```

The challenge was balancing:

- speed vs. process;
- learning vs. building;
- low cost vs. automation;
- tool-agnostic architecture vs. tool-specific execution;
- future multi-agent readiness vs. current sequential workflow.

## External Influence

The workflow was heavily influenced by experienced practitioners discussing harness engineering, especially patterns from Pwnage/OmniWatcher-style project setup:

- interview → spec docs → harness → implementation plan → milestone → issues → implementation;
- repo-owned context instead of hidden chat memory;
- rules, skills, hooks, and subagent roles;
- issue-backed PRs;
- documentation versioning and consistency;
- friction logging;
- staged issue creation instead of planning every future task upfront.

The key adaptation was reducing those ideas to a smaller MVP scope and a lower-cost sequential workflow.

## Final Workflow Model

The final SquadSync model is:

```text
Human + ChatGPT GitHub
  -> architecture, planning, docs, issues, PR setup, review support

GitHub
  -> durable source of truth for docs, issues, PRs, ADRs, and workflow state

Codex CLI
  -> primary implementation worker for scoped agent-ready issues

Human owner
  -> architecture authority, reviewer, and merge authority
```

This is better described as sequential agentic workflow than parallel multi-agent automation.

## What Was Built

The harness foundation now includes:

- `AGENTS.md` as the root agent operating map.
- `PLANS.md` as the ExecPlan standard for complex work.
- `.agents/skills/` as native Codex skill entry points.
- `.codex/` as a placeholder for future Codex-native configuration.
- `docs/agentic-workflow/` as the human-readable workflow architecture.
- `docs/agentic-workflow/tools/chatgpt-github/` for ChatGPT GitHub workflows.
- `docs/agentic-workflow/tools/codex-cli/` for Codex CLI profiles, rules, skills, hooks, and planned subagents.
- Scoped `AGENTS.md` files for `apps/api`, `apps/web`, `infra`, and `docs`.
- Context-management and prompting standards.
- Validation gates and stop conditions.
- Documentation governance and root summary sync rules.
- A harness friction log for future improvement.

## Key Design Decisions

### Keep generic policy separate from tool-specific implementation

SquadSync uses a layered model:

```text
docs/agentic-workflow/policy
  -> durable project rules

docs/agentic-workflow/workflow
  -> generic lifecycle and validation flow

docs/agentic-workflow/specs
  -> issue and PR contracts

docs/agentic-workflow/tools
  -> tool-specific profiles
```

This preserved the original goal of modularity without pretending every tool can share identical operational files.

### Treat Codex CLI as the primary implementation tool

The original plan included ChatGPT, Codex, and GitHub Copilot. After reviewing experienced feedback, GitHub Copilot was removed from the formal workflow. It may still be used locally, but it is not part of the canonical harness.

This simplified the agentic stack and aligned better with repeatability.

### Add native Codex skills

A major correction was recognizing that human-readable skill docs are not the same as native Codex skills.

The repo now distinguishes:

```text
docs/agentic-workflow/tools/codex-cli/skills
  -> expanded playbooks

.agents/skills
  -> native Codex skill entry points
```

### Defer executable hooks and active subagents

Hooks, command-policy rules, MCP, and active subagents were documented as future areas but not activated.

This avoided pretending the repo had automation that had not been tested.

### Use scoped `AGENTS.md` files

Local `AGENTS.md` files were added to implementation zones so future agents can load area-specific context.

Current statuses:

- `apps/api/AGENTS.md`: active for Phase 1.
- `docs/AGENTS.md`: active for documentation work.
- `apps/web/AGENTS.md`: placeholder for future frontend work.
- `infra/AGENTS.md`: placeholder for future infrastructure work.

## Trade-offs

### Benefit: stronger implementation foundation

The repo is now much more navigable for agents and humans. Future work can reference durable project docs instead of reconstructing decisions from chat.

### Cost: delayed coding

The biggest trade-off was time. Phase 0 delayed the API scaffold and AWS learning work.

### Risk: over-documentation

The repo now has a lot of documentation. The mitigation was to add a Phase 0 closeout guide, active/placeholder labels, and scoped navigation.

### Risk: untested harness assumptions

The harness is designed but not yet proven. Phase 1 must test whether Codex can actually use the structure effectively.

## What Was Deferred

Deferred intentionally:

- executable Codex hooks;
- native command-policy `.rules` files;
- active subagent automation;
- MCP configuration;
- CI/CD automation;
- Codex automations;
- cloud deployment;
- fully autonomous sprint execution.

These should be added only after implementation friction proves the need.

## Lessons Learned

Harness engineering is not just prompt writing. It is the discipline of turning repeated context, workflow rules, validation expectations, and handoff patterns into durable project assets.

The most important distinction was between:

```text
human-readable workflow docs
native tool configuration
actual executable automation
```

Those layers should be related but not confused.

Another lesson was that tool-agnostic design has limits. High-level policy, specs, and workflow can be modular and portable. Operational details such as skills, hooks, CLI behavior, and config need tool-specific implementation.

## Current Relevance

This work is directly relevant to modern software development because agentic tooling is becoming part of the development workflow. The project now demonstrates awareness of:

- context engineering;
- prompt engineering;
- agent workflow design;
- Codex-native skills and plans;
- documentation governance;
- issue-backed implementation;
- human-in-the-loop AI development;
- cost-aware AI tooling strategy.

For portfolio purposes, this is a stronger signal than simply using AI to generate code. It shows an attempt to create a repeatable engineering system.

## Next Step

Phase 1 should validate the harness by using Codex CLI on a real task:

```text
Initialize API scaffold under apps/api
```

Success means Codex can use repo-owned instructions, native skills, scoped `AGENTS.md`, issue scope, and validation expectations to create a clean, reviewable implementation PR.

If it fails or creates confusion, the friction should be recorded and used to refine the harness.