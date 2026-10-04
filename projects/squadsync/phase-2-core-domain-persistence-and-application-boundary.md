# SquadSync Phase 2 — Core Domain, Persistence, and Application Boundary

## Entry Metadata

| Field | Value |
|---|---|
| Title | SquadSync Phase 2 — Core Domain, Persistence, and Application Boundary |
| Type | Project Retrospective |
| Subject | SquadSync |
| Date | 2026-10-03 |
| Status | Complete |
| Phase | Phase 2 — Core Domain and Persistence |
| Sprint | Sprints 3–6 |
| Issue | Sprint trackers #82, #94, #102, #109 |
| Tags | domain-modeling, clean-architecture, ef-core, postgresql, dependency-inversion, integration-testing |
| Related Repo | https://github.com/bbubb/squadsync |
| Related Work | PRs #93, #101, #104, #107, #108, #112, #113 |

## Summary

Phase 2 turned SquadSync's API foundation into a persisted domain model with its first real Application workflow.

The phase established the core team-and-roster concepts needed for the MVP: represented Users, Teams, TeamMemberships with constrained TeamRoles, person-level PlayerProfiles, and team-specific RosterEntries. Those concepts are persisted through EF Core and PostgreSQL, validated with both unit tests and opt-in real-database tests, and exercised through an explicit Development demo scenario.

The most important result was architectural clarity. Domain, Application, Infrastructure, and PostgreSQL now each have distinct responsibilities that are enforced in working code rather than only described in documentation.

## Context

Phase 1 established the .NET 10 solution, Clean Architecture project boundaries, PostgreSQL connectivity, Docker Compose, CI, health checks, and testing baseline. Phase 2 needed to add real domain state without coupling the Domain project to EF Core or allowing persistence concerns to define the model.

The work was intentionally incremental. Each slice introduced one meaningful part of the model, mapped it explicitly in Infrastructure, generated an additive migration, and validated the result against PostgreSQL before expanding further.

## Key Decisions

### Model team participation explicitly

`TeamMembership` became the relationship between a represented `User` and a `Team`, with one constrained `TeamRole`.

This keeps the MVP simple while making team participation a first-class concept instead of an incidental database join. PostgreSQL enforces the structural relationship and uniqueness of one membership per User/Team pair.

### Separate player identity from roster context

`PlayerProfile` stores person-level soccer attributes, while `RosterEntry` stores data that belongs to a player's membership on a specific team.

That distinction prevents team-specific state such as jersey number or roster status from being mixed with attributes of the person. `RosterEntry` points to `TeamMembership`, so the model does not duplicate the User-to-Team relationship.

### Put different rules in the layer that can actually know them

Phase 2 produced a practical rule-of-thumb that I expect to reuse:

- **Domain** protects intrinsic object validity.
- **Application** coordinates rules that require other domain state.
- **PostgreSQL** protects relational structure and uniqueness.
- **Infrastructure** translates between the application and persistence technology.

The clearest example is roster eligibility. A `RosterEntry` can validate its own ID, jersey number, and status, but it cannot know whether its membership is actually a Player membership.

That rule therefore lives in the `AddPlayerToRoster` Application use case:

```text
AddPlayerToRoster
    -> load TeamMembership
    -> require TeamRole.Player
    -> construct RosterEntry
    -> persist through an Application-owned contract
```

### Keep persistence technology in Infrastructure

EF Core, Npgsql, migrations, provider-specific constraint handling, and PostgreSQL exception translation all remain in `SquadSync.Infrastructure`.

The Domain project has no EF Core dependency. Application depends on a small persistence capability, `IRosterPersistence`, while Infrastructure implements it with `EfRosterPersistence`.

This was the first complete working example in SquadSync of dependency inversion across the Application/Infrastructure boundary.

## What Changed / What I Learned

### Clean Architecture became concrete

The project boundaries now do more than organize files. They constrain dependencies:

```text
API -> Application
API -> Infrastructure

Infrastructure -> Application
Infrastructure -> Domain

Application -> Domain

Domain -> no outer project
```

That makes architectural violations harder to introduce accidentally and provides a clear place for future HTTP, persistence, and external-service behavior.

### Persistence is more than "the entity saved"

The PostgreSQL tests deliberately validate behavior that unit tests cannot prove:

- fresh EF materialization after clearing tracking;
- foreign-key and uniqueness enforcement;
- readable enum storage;
- restrictive delete behavior;
- rollback cleanup;
- provider-specific conflict translation.

This gave the phase a stronger definition of "working persistence" than simply calling `SaveChangesAsync`.

### Abstractions are more useful when driven by a use case

Rather than creating a repository for every entity by convention, the first Application-owned persistence abstraction was introduced when `AddPlayerToRoster` needed one.

That kept the interface small and tied to an actual capability the Application layer consumes.

### Demo data should be intentional

Phase 2 also added an explicit Development-only demo seed command. It is opt-in, idempotent, non-destructive, and does not mutate the database during ordinary startup.

That provides a useful local inspection/demo scenario without turning bootstrap data into hidden production behavior.

## Trade-offs

The MVP intentionally favors explicit, narrow concepts over a generalized role or authorization system. One TeamMembership currently has one TeamRole, which is sufficient for the current coach/roster workflow but may need to evolve as requirements become more complex.

Ordinary tests also remain database-independent, while real PostgreSQL validation is opt-in. This keeps the fast development loop lightweight while still providing provider-specific evidence when schema and persistence behavior matter.

Delete behavior is restrictive rather than cascading. That avoids silently destroying related data before the product has defined the correct archive/delete workflows.

## Lessons Learned

- A domain model is easier to reason about when person-level data and relationship-specific data have separate lifecycles.
- Business rules should live where the information needed to enforce them is available.
- Infrastructure is allowed to be technology-specific; the architectural goal is to prevent those details from leaking inward.
- Dependency inversion is clearer when it emerges from a real use case rather than from a repository template.
- Real database tests are most valuable when they prove behavior that in-memory/unit tests cannot.
- Foundation work should stop once the pattern is proven. The next phase should turn these boundaries into usable product behavior.

## Current Relevance

For my portfolio, Phase 2 demonstrates practical backend and architecture skills rather than only framework familiarity:

- domain and relational modeling;
- Clean Architecture dependency boundaries;
- EF Core and PostgreSQL migrations;
- application-layer orchestration;
- dependency injection and dependency inversion;
- integration testing against a real database;
- constraint/error translation across layers;
- safe development bootstrap workflows.

These are directly relevant to backend, integration, cloud, systems, solutions-engineering, and architecture-oriented roles because the work connects code-level decisions to system boundaries and operational behavior.

## Next Steps

Phase 3 will expose the completed team-and-roster model through a usable HTTP API.

The next planning step is to define the smallest coherent set of Team, membership, PlayerProfile, and roster operations needed for a coach-facing workflow, including request/response validation, consistent errors, and HTTP integration tests.

Authentication, frontend work, match planning, and later service integrations remain outside that immediate scope.
