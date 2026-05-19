# SquadSync: Platform and Service Boundaries

## Snapshot
- **Project:** SquadSync + Soccer-Subber integration
- **Focus:** Separating core platform responsibilities from specialized domain services
- **Core problem:** Decide what belongs in the main SquadSync platform versus what should become a separate service.
- **What this demonstrates:** Service-boundary thinking, integration design, MVP scoping, and cloud-readiness.

## Summary
As SquadSync grew conceptually, it became clear that not every soccer-related capability should live inside the core platform. Roster, team, user, role, and event management belong naturally in SquadSync. Lineup and substitution logic is specialized decision logic and fits better as a separate Soccer-Subber service. This boundary creates a cleaner architecture and a stronger path toward event-driven cloud integration.

## Problem & Constraints
The original SquadSync vision could expand in many directions: team management, tactical planning, communication, roles, permissions, scheduling, player profiles, stats, lineup optimization, notifications, and AI explanations.

The risk was turning SquadSync into a broad monolith before the MVP proved a single high-value workflow. The better architecture is to keep SquadSync as the system of record for team context and let specialized services handle specialized logic.

The current C# backend already shows a platform-style foundation: users, teams, players, org units, roles, permissions, activities, locations, sites, and features are represented in the database context. :contentReference[oaicite:8]{index=8} That supports the idea that SquadSync is the core platform layer, not necessarily the place where every algorithm should live.

## Decisions & Trade-offs
- **Decision:** Treat SquadSync as the core platform/system of record.  
  **Why:** Users, teams, roles, rosters, and events require durable relational state and platform-level authorization.  
  **Trade-off:** The core platform must stay disciplined and avoid absorbing every domain feature.

- **Decision:** Treat Soccer-Subber as a separate decision service.  
  **Why:** Lineup generation has different logic, iteration speed, and future AI/optimization potential.  
  **Trade-off:** Integration becomes more complex because SquadSync must call or message another service.

- **Decision:** Use event-driven notification as a separate future workflow.  
  **Why:** Notifications are naturally reactive: roster changed, lineup generated, event updated, player notified.  
  **Trade-off:** Adds async infrastructure and delivery-state concerns, but creates a better cloud architecture story.

- **Decision:** Keep AI explanation as a later layer, not the source of truth.  
  **Why:** The lineup/substitution engine should remain deterministic and debuggable first.  
  **Trade-off:** AI value is delayed, but trust and testability improve.

## Lessons & Next Moves
The key lesson is that service boundaries should follow responsibility boundaries. SquadSync should own durable team context. Soccer-Subber should own lineup decision logic. Notifications should react to events. AI should explain or summarize outputs after the deterministic logic runs.

The next implementation should prove one clean integration slice:

1. Coach creates or selects a roster in SquadSync.
2. SquadSync sends roster, formation, and match settings to Soccer-Subber.
3. Soccer-Subber returns a lineup/substitution plan.
4. SquadSync stores or displays the result.
5. SquadSync emits a lineup-generated event.
6. Notification service reacts.

That single flow demonstrates full-stack, service integration, event-driven architecture, and future cloud migration without overbuilding the entire platform.

## Evidence
- **Repo:** `bbubb/squadsync_csharp`
- **Related repo:** `bbubb/soccer-subber`
- **Related files:** `SQLDbContext.cs`, Soccer-Subber Python lineup files from commit history
- **Follow-up:** Create a diagram showing SquadSync API → Soccer-Subber service → event/notification flow.