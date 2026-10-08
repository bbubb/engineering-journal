# Legacy SquadSync — Deciding Where Specialized Logic Belongs

**Architecture Reflection** · SquadSync · Historical design exploration · May 2026

<details>
<summary>Metadata & related work</summary>

| Field | Value |
|---|---|
| Status | Complete — retrospective |
| Tags | modular-monolith, service-boundaries, integration, scope-control |
| Source basis | Earlier SquadSync architecture exploration |
| Public follow-up | https://github.com/bbubb/squadsync |

</details>

## Summary

As the early SquadSync concept expanded, one recurring question was whether specialized lineup and substitution planning belonged inside the team-management application. The design direction separated durable team and match data from algorithmic lineup assistance, with Soccer-Subber as a distinct integration candidate.

The result was a clear architectural direction: define the boundary first and introduce remote integration only when it serves a working product.

## At a Glance

- Distinguished core team-management responsibilities from specialized recommendation logic.
- Chose explicit integration boundaries while preserving a modular-monolith core.
- Deferred remote services, notifications, and AI explanations until a usable workflow justified them.

## Key Themes / Reflections

### Responsibility matters more than the number of deployed services

SquadSync needs to own team, membership, roster, match, and lineup state. Soccer-Subber is concerned with suggesting assignments under soccer-specific constraints. That difference provides a useful contract boundary even before both applications are deployed separately.

Separating this specialized logic made sense for the product, but it also created an opportunity to develop integration and cloud-engineering skills. I wanted to explore APIs, service contracts, and eventually AWS deployment patterns through a meaningful component, rather than add cloud services to SquadSync without a reason. The lesson was to establish a useful boundary first; independent deployment and distributed infrastructure still need to justify their operational cost.

### Future integration should not dictate the first implementation

A narrow request/response contract can isolate suggestion logic from the core platform without prematurely committing to a cloud provider or communication mechanism. Reactive notifications and optional AI explanations could follow after the core workflow and integration contract were proven.

The trade-off is that a bounded interface leaves some integration decisions unresolved. That is preferable to paying the complexity cost of distributed behavior before the coach-facing experience works.

## Why It Matters

The lasting principle is to make ownership explicit and introduce infrastructure deliberately. A bounded Soccer-Subber integration could both preserve a simpler SquadSync core and provide practical experience with APIs, cloud deployment, failure handling, and cost-aware system design.
