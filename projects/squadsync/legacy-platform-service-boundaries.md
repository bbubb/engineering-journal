# Legacy SquadSync Platform and Service Boundaries

## Entry Metadata

| Field | Value |
|---|---|
| Title | Legacy SquadSync Platform and Service Boundaries |
| Type | Architecture Reflection |
| Subject | SquadSync |
| Date | 2026-05-19 |
| Status | Complete |
| Phase | N/A |
| Sprint | N/A |
| Issue | N/A |
| Tags | service-boundaries, integration, soccer-subber, event-driven-design, mvp-scope |
| Related Repo | bbubb/squadsync_csharp |
| Related Work | SquadSync and Soccer-Subber integration planning |

## Summary

As SquadSync grew conceptually, it became clear that not every soccer-related capability should live inside the core platform.

Roster, team, user, role, and event management belong naturally in SquadSync. Lineup and substitution logic is specialized decision logic and fits better as a separate Soccer-Subber service.

## Context

The original SquadSync vision could expand into team management, tactical planning, communication, roles, permissions, scheduling, player profiles, stats, lineup optimization, notifications, and AI explanations.

The risk was turning SquadSync into a broad monolith before the MVP proved a single high-value workflow.

## Key Decisions

### Treat SquadSync as the system of record

SquadSync should own durable team context: users, teams, roles, rosters, events, and authorization boundaries.

### Treat Soccer-Subber as a separate decision service

Lineup generation has different logic, iteration speed, testing concerns, and future AI/optimization potential. Keeping it separate preserves a cleaner boundary.

### Keep notifications reactive

Notifications should eventually react to events such as roster changes, lineup generation, or event updates.

### Keep AI explanation as a later layer

The lineup/substitution engine should remain deterministic and debuggable first. AI can explain or summarize outputs later without becoming the source of truth.

## Trade-offs

Service boundaries add integration complexity, but they clarify ownership and make the architecture easier to evolve.

A monolith would be faster initially, but it would hide the distinction between durable platform state and specialized decision logic.

## Lessons Learned

Service boundaries should follow responsibility boundaries. SquadSync owns durable team context. Soccer-Subber owns lineup decision logic. Notifications react to events.

## Current Relevance

This boundary directly informs the current public SquadSync rebuild and the planned Soccer-Subber integration.

## Next Steps

The future MVP should prove one clean integration slice: roster context from SquadSync, lineup result from Soccer-Subber, and eventual event/notification behavior.
