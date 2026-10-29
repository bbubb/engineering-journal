# Legacy SquadSync — Service Boundaries: Separating Team Data from Lineup Logic

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

As the early SquadSync concept expanded, one recurring question was whether specialized lineup and substitution planning belonged inside the team-management application. The plan was for SquadSync to store team, roster, and match data while Soccer-Subber calculated suggested lineups.

I wanted to define which application owned each responsibility before deciding whether they needed to communicate over a network.

## Key Outcomes

- Distinguished core team-management responsibilities from specialized recommendation logic.
- Kept SquadSync as a modular monolith while planning a separate interface for lineup suggestions.
- Deferred remote services, notifications, and AI explanations until a usable workflow justified them.

## Engineering Reflections

### Team data and lineup suggestions have different owners

SquadSync needs to own team, membership, roster, match, and lineup state. Soccer-Subber is concerned with suggesting assignments under soccer-specific constraints. That distinction is useful even before the two applications run as separately deployed services.

Separating this specialized logic made sense for the product, but it also created an opportunity to develop integration and cloud-engineering skills. I wanted to explore APIs, service contracts, and eventually AWS deployment patterns through a meaningful component, rather than add cloud services to SquadSync without a reason. I learned to define the interface first, then decide whether a separate deployment is worth the cost of network calls, failures, and operations.

### A separate code boundary did not require a deployed microservice

A small request/response contract would let SquadSync ask for a lineup suggestion without giving Soccer-Subber direct access to its database. I did not need to choose AWS services or event infrastructure to define that boundary. Reactive notifications and optional AI explanations could follow after the core workflow and integration contract were proven.

This left deployment and failure-handling details for later, but avoided building a distributed system before the coach-facing workflow existed.

## Significance

I learned to separate responsibilities in code first, then add network calls or cloud services when the working product needs them. Soccer-Subber can still provide meaningful API and cloud-integration experience without making SquadSync itself a distributed system too early.
