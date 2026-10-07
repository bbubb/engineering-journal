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
| Tags | domain-modeling, clean-architecture, ef-core, postgresql, dependency-inversion, integration-testing, agentic-workflow |
| Related Repo | https://github.com/bbubb/squadsync |
| Related Work | PRs #93, #101, #104, #107, #108, #112, #113 |

## Summary

Phase 2 moved SquadSync from an API foundation into a persisted domain model with its first real Application workflow.

The project now models Users, Teams, TeamMemberships with constrained TeamRoles, person-level PlayerProfiles, and team-specific RosterEntries. Those concepts are persisted through EF Core and PostgreSQL, tested both in isolation and against a real database, and exercised through an explicit Development demo scenario.

The bigger outcome for me was architectural clarity. I already had experience with controllers, services, repositories, interfaces, and domain concepts. What became clearer was how Clean Architecture turns some logical separations into physically enforced dependency boundaries, and how a more domain-driven process changes the order in which a system is built.

## Context

Phase 1 established the .NET 10 solution, Clean Architecture project boundaries, PostgreSQL connectivity, Docker Compose, CI, health checks, and testing baseline. Phase 2 added real domain state without letting EF Core or the database define the model.

The implementation rhythm felt unusually granular at first. Small contracts were defined, implemented, tested, persisted, and validated before the next piece was added. For simple entities and relationships, that sometimes felt overly formal.

By the end of the phase, those pieces had started to compose into something more recognizable: a domain model, persistence layer, Application use case, and repeatable demo data. I am still evaluating the trade-off, but I can see the value in building complexity on top of assumptions that have already been tested.

## Key Decisions

### Model team participation explicitly

`TeamMembership` represents the relationship between a `User` and a `Team`, with one constrained `TeamRole`.

This makes team participation a first-class business concept instead of an incidental database join. It also reinforced an important modeling lesson: sometimes the relationship between two concepts carries enough identity, state, or rules to deserve its own model.

### Separate player identity from roster context

`PlayerProfile` stores person-level soccer attributes, while `RosterEntry` stores information that belongs to a player's membership on a particular team.

That keeps attributes such as dominant foot separate from team-specific state such as jersey number or roster status. It also gave me a concrete way to understand concepts such as domain state and relationship-specific state rather than treating them as abstract terminology.

### Put rules where the necessary information exists

Phase 2 produced a practical rule-of-thumb:

- **Domain** owns business concepts, state, and rules intrinsic to those concepts.
- **Application** coordinates use cases and rules that require multiple pieces of domain state.
- **PostgreSQL** protects relational structure and uniqueness.
- **Infrastructure** implements the technical capabilities needed by the application.

For example, a `RosterEntry` can validate its own state, but it cannot know whether the related TeamMembership represents a Player. That cross-record rule belongs in the `AddPlayerToRoster` Application use case:

```text
AddPlayerToRoster
    -> load TeamMembership
    -> require TeamRole.Player
    -> construct RosterEntry
    -> persist through an Application-owned contract
```

I understood business logic and technical layering before this phase, but examples like this made the distinction between a Domain rule and an Application workflow rule much more precise.

### Keep persistence technology at the Infrastructure boundary

EF Core, Npgsql, migrations, and PostgreSQL-specific behavior remain in `SquadSync.Infrastructure`. The Domain project has no EF Core dependency.

Concrete technology is not the problem; it simply belongs at the boundary where it can be changed or tested without becoming part of the core business model.

## What Changed / What I Learned

### Clean Architecture became more structural than conceptual

I was already comfortable with controller/service/repository-style layering. The significant shift was seeing how separate projects make dependency direction explicit:

```text
API -> Application
API -> Infrastructure
Infrastructure -> Application
Infrastructure -> Domain
Application -> Domain
Domain -> no outer project
```

Application cannot casually reach into EF Core or PostgreSQL without changing the project dependency itself. The architecture can still be violated, but doing so becomes a visible design change rather than an incidental reference.

I am also beginning to distinguish being **domain-oriented** from being more deliberately **domain-driven**. My earlier development experience already involved domain thinking, but this process lets business concepts and use cases more directly influence both structure and implementation sequence.

### Dependency Inversion became concrete through a real use case

Rather than creating a repository abstraction for every entity, the first Application-owned persistence contract appeared when `AddPlayerToRoster` actually needed one:

```text
AddPlayerToRoster
        -> IRosterPersistence
        <- EfRosterPersistence
```

This helped me see Dependency Inversion as more than "use an interface." The higher-level Application layer defines the capability its workflow needs; Infrastructure supplies the technical implementation.

That mental model is clearer now, although I expect to refine it as the Application surface grows.

### Validation and the agentic workflow started to reinforce each other

The project is not following strict test-first TDD. The working pattern has been to define intended behavior, implement a small slice, add focused tests, validate the relevant boundary, and merge only when there is evidence that the assumptions hold.

The real PostgreSQL tests are useful because they prove behavior unit tests cannot: actual materialization, relationships, uniqueness, rollback behavior, and provider-specific persistence assumptions.

At the same time, the issue/PR workflow became genuinely productive. Issues define bounded intent and acceptance criteria, Codex performs scoped implementation and returns a draft PR, and I remain responsible for understanding the design, reviewing the tests and trade-offs, and deciding whether the work should merge.

That is beginning to feel less like using AI to generate code and more like an accelerated engineering workflow. Reviewing real implementations has also made architecture terminology easier to understand than learning it only as definitions.

## Trade-offs

The MVP intentionally favors explicit, narrow concepts over a generalized role or authorization system. One TeamMembership currently has one TeamRole, which is sufficient for the current scope but may need to evolve later.

The development process also carries overhead. Small, heavily validated slices can make progress feel slower than designing a larger system structure up front. The potential benefit is a foundation that is easier to trust as complexity increases. Phase 3 should provide a better test of whether that trade-off continues to pay off when the project begins delivering complete HTTP workflows.

Ordinary tests remain database-independent while real PostgreSQL validation is opt-in, keeping the normal development loop fast without losing provider-specific evidence when it matters.

## Lessons Learned

- Logical layering and Clean Architecture can share many principles, but CA project boundaries make dependency direction more explicit and enforceable.
- Being domain-oriented is not necessarily the same as letting the domain drive implementation boundaries and sequence.
- Person-level data and relationship-specific data often have different lifecycles and should be modeled accordingly.
- Business rules should live where the information needed to enforce them is available.
- Dependency Inversion becomes easier to understand when it solves a real use-case dependency instead of appearing as an abstract pattern.
- A slice is not finished merely because it compiles; validation should prove the assumptions that matter at that boundary.
- The issue -> implementation -> draft PR -> human review loop can combine AI acceleration with continued human ownership and learning.

## Current Relevance

At a glance, Phase 2 shows that SquadSync has moved beyond scaffolding into real backend design: a persisted domain model, tested relational behavior, an Application workflow, and a repeatable development scenario.

For a deeper technical read, the work demonstrates domain and relational modeling, Clean Architecture dependency boundaries, EF Core/PostgreSQL migrations, application orchestration, Dependency Injection and developing Dependency Inversion understanding, and real-database integration testing.

For me, the phase reflects a shift from recognizing patterns such as layers, services, repositories, and interfaces toward understanding more precisely why those boundaries exist, which direction dependencies should point, and how business concepts can drive implementation decisions.

## Next Steps

Phase 3 will expose the completed team-and-roster model through a usable HTTP API.

That should also provide a useful test of several ideas that are still developing for me: how Application services and interfaces evolve as the surface grows, whether domain/use-case organization remains intuitive at larger scale, and whether the additional Clean Architecture structure continues to justify its overhead once the project is delivering complete features.
