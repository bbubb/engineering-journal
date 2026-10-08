# Legacy SquadSync — Deciding Where Specialized Logic Belongs

**Architecture Reflection** · SquadSync · Historical design exploration · May 2026

<details>
<summary>Metadata & related work</summary>

| Field | Value |
|---|---|
| Status | Complete — retrospective |
| Tags | modular-monolith, service-boundaries, integration, scope-control |
| Source basis | Historical SquadSync design discussions and private prototype |
| Public follow-up | https://github.com/bbubb/squadsync |

</details>

## Summary

As the early SquadSync concept expanded, one recurring question was whether specialized lineup and substitution planning belonged inside the team-management application. The design direction separated durable team and match data from algorithmic lineup assistance, with Soccer-Subber as a distinct integration candidate.

This was a boundary decision, not evidence that a production distributed system or event-notification pipeline had already been deployed.

## At a Glance

- Distinguished core team-management responsibilities from specialized recommendation logic.
- Chose explicit integration boundaries while preserving a modular-monolith core.
- Deferred remote services, notifications, and AI explanations until a usable workflow justified them.

## Key Themes / Reflections

### Responsibility matters more than the number of deployed services

SquadSync needs to own team, membership, roster, match, and lineup state. Soccer-Subber is concerned with suggesting assignments under soccer-specific constraints. That difference provides a useful contract boundary even before both applications are deployed separately.

I initially saw external services as an attractive way to demonstrate architecture. The more disciplined lesson is that a clean module boundary does not automatically require network calls, independent deployment, or event infrastructure. A separate runtime should be justified by its operating or development needs.

### Future integration should not dictate the first implementation

A narrow request/response contract can isolate suggestion logic from the core platform without prematurely committing to a cloud provider or communication mechanism. Reactive notifications and optional AI explanations remained future possibilities, not implemented outcomes of the early prototype.

The trade-off is that a bounded interface leaves some integration decisions unresolved. That is preferable to paying the complexity cost of distributed behavior before the coach-facing experience works.

## Why It Matters

The lasting principle is to make ownership explicit and defer infrastructure until it solves a demonstrated problem. The current public SquadSync architecture documents a modular monolith, a planned Soccer-Subber port, and later event-readiness work; those are future-facing decisions rather than claims about the private prototype's delivered capabilities.
