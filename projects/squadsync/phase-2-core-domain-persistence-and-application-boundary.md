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

The project now models represented Users, Teams, TeamMemberships with constrained TeamRoles, person-level PlayerProfiles, and team-specific RosterEntries. Those concepts are persisted through EF Core and PostgreSQL, tested both in isolation and against a real database, and exercised through an explicit Development demo scenario.

The bigger outcome for me was architectural clarity. I already had experience with controllers, services, repositories, interfaces, and domain concepts. What became clearer in this phase was how Clean Architecture turns some of those logical separations into physically enforced dependency boundaries, and how a more domain-driven implementation process changes the order in which the system is built.

## Context

Phase 1 established the .NET 10 solution, Clean Architecture project boundaries, PostgreSQL connectivity, Docker Compose, CI, health checks, and testing baseline. Phase 2 needed to add real domain state without letting EF Core or the database dictate the model.

The implementation rhythm felt unusually granular at first. Small contracts were defined, implemented, tested, persisted, and validated before the next piece was added. For relatively simple entities and relationships, that sometimes felt overly formal compared with designing a broader structure up front.

By the end of the phase, those pieces had started to compose into something more recognizable: a domain model, persistence layer, first Application use case, and repeatable demo data. I am still evaluating the trade-off, but I can see the appeal of allowing complexity to grow on top of assumptions that have already been exercised rather than discovering foundational problems only after a much larger structure is in place.

## Key Decisions

### Model team participation explicitly

`TeamMembership` represents the relationship between a `User` and a `Team`, with one constrained `TeamRole`.

This makes team participation a first-class business concept instead of an incidental database join. It also helped reinforce a useful modeling lesson: sometimes the relationship between two concepts has enough identity, state, or rules that it deserves to be modeled explicitly.

### Separate player identity from roster context

`PlayerProfile` stores person-level soccer attributes, while `RosterEntry` stores information that belongs to a player's membership on a particular team.

That distinction keeps attributes such as dominant foot separate from team-specific state such as jersey number or roster status. It also gave me a much more concrete understanding of terms such as domain state and relationship-specific state: the vocabulary becomes easier to understand when it maps to an actual modeling decision.

### Put rules where the necessary information exists

One of the clearest lessons from Phase 2 was that different layers protect different kinds of truth:

- **Domain** owns business concepts, state, and rules intrinsic to those concepts.
- **Application** coordinates use cases and rules that require multiple pieces of domain state.
- **PostgreSQL** protects relational structure and uniqueness.
- **Infrastructure** implements the technical capabilities needed by the application.

The clearest example is roster eligibility. A `RosterEntry` can validate its own ID, jersey number, and status, but it cannot know whether its related TeamMembership represents a Player.

That cross-record rule belongs in the `AddPlayerToRoster` Application use case:

```text
AddPlayerToRoster
    -> load TeamMembership
    -> require TeamRole.Player
    -> construct RosterEntry
    -> persist through an Application-owned contract
```

I understood business logic and technical layering before this phase, but examples like this have made the difference between Domain rules and Application workflow rules much more precise.

### Keep persistence technology at the Infrastructure boundary

EF Core, Npgsql, migrations, and PostgreSQL-specific behavior remain in `SquadSync.Infrastructure`. The Domain project has no EF Core dependency.

The important point is not that concrete technology is undesirable. It has to exist somewhere. The value is that the core business code does not need to understand how PostgreSQL or EF Core work in order to express the domain or execute an Application workflow.

## What Changed / What I Learned

### Clean Architecture became more structural than conceptual

I was already comfortable with controller/service/repository-style layering. The significant shift was seeing how separate projects can make dependency direction explicit.

```text
API -> Application
API -> Infrastructure

Infrastructure -> Application
Infrastructure -> Domain

Application -> Domain

Domain -> no outer project
```

Application cannot casually reach into EF Core or PostgreSQL without first changing the project dependency itself. That does not make the architecture impossible to violate, but it turns an accidental dependency into a visible architectural change.

I am also beginning to distinguish being **domain-oriented** from being more deliberately **domain-driven**. My earlier development experience already involved substantial domain thinking, but this process lets business concepts and use cases more directly influence both the structure and sequencing of implementation. I am interested to see whether that remains clearer as the codebase grows.

### Dependency Inversion became concrete through a real use case

Rather than creating a repository abstraction for every entity by convention, the first Application-owned persistence contract appeared when `AddPlayerToRoster` actually needed one:

```text
AddPlayerToRoster
        -> IRosterPersistence
        <- EfRosterPersistence
```

This helped me understand Dependency Inversion as more than simply "use an interface." The higher-level Application layer defines the capability its workflow needs; Infrastructure supplies the technical implementation.

I understand that example better now, although interface ownership and Dependency Inversion are still concepts I expect to refine as the Application surface grows.

### Validation became part of the definition of a finished slice

This project is not following strict test-first TDD. The working pattern has been closer to defining intended behavior, implementing a small slice, adding focused tests, validating the relevant boundary, and merging only when there is evidence that the assumptions hold.

The real PostgreSQL tests are especially useful because they prove things unit tests cannot: actual materialization, relationships, uniqueness, rollback behavior, and provider-specific persistence assumptions.

The small slices sometimes felt trivial individually. In aggregate, I can now see why the discipline may matter: later behavior is being built on components whose most important assumptions have already been tested.

### The human-guided agentic workflow is becoming productive

The issue/PR workflow is probably the most novel part of my development process during this phase.

Issues define bounded intent and acceptance criteria, Codex performs scoped implementation and returns a draft PR, and I remain responsible for understanding the design, reviewing the changes and tests, and deciding whether the work should merge.

That is beginning to feel less like using AI to generate code and more like an accelerated engineering workflow. It has also become part of the learning process: reviewing real implementations has made terms such as domain state, persistence, application orchestration, and dependency inversion easier to understand than learning them only as definitions.

## Trade-offs

The MVP intentionally favors explicit, narrow concepts over a generalized role or authorization system. One TeamMembership currently has one TeamRole, which is sufficient for the current scope but may need to evolve as requirements become more complex.

The development process itself also carries overhead. Small, heavily validated slices can make progress feel slower and less visually organized than designing a larger system structure up front. The potential benefit is a foundation that is easier to trust as complexity increases. Phase 3 should provide a better test of whether that trade-off continues to pay off when the project begins delivering complete HTTP workflows.

Ordinary tests remain database-independent while real PostgreSQL validation is opt-in. This keeps the normal development loop fast while still allowing provider-specific behavior to be proven when it matters.

## Lessons Learned

- Logical layering and Clean Architecture can share many principles, but CA project boundaries make dependency direction more explicit and enforceable.
- Being domain-oriented is not necessarily the same as letting the domain drive implementation boundaries and sequence.
- Person-level data and relationship-specific data often have different lifecycles and should be modeled accordingly.
- Business rules should live where the information needed to enforce them is available.
- Infrastructure can be concrete; the architectural goal is to prevent technical details from leaking inward.
- Dependency Inversion becomes easier to understand when it solves a real use-case dependency instead of appearing as an abstract pattern.
- A slice is not finished merely because it compiles; validation should prove the assumptions that matter at that boundary.
- The issue -> implementation -> draft PR -> human review loop can combine AI acceleration with continued human ownership and learning.

## Current Relevance

For a hiring manager, Phase 2 shows that SquadSync has moved beyond scaffolding into real backend design: a persisted domain model, tested relational behavior, an Application workflow, and a repeatable development scenario.

For a technical reviewer, the work provides deeper evidence of domain and relational modeling, Clean Architecture dependency boundaries, EF Core/PostgreSQL migrations, application orchestration, Dependency Injection and developing Dependency Inversion understanding, real-database integration testing, and disciplined scope control.

For me, the phase reflects a broader shift from recognizing patterns such as layers, services, repositories, and interfaces toward understanding more precisely why those boundaries exist, which direction dependencies should point, and how business concepts can drive implementation decisions.

## Next Steps

Phase 3 will expose the completed team-and-roster model through a usable HTTP API.

The next planning step is to define the smallest coherent set of Team, membership, PlayerProfile, and roster operations needed for a coach-facing workflow, including request/response validation, consistent errors, and HTTP integration tests.

It should also provide a useful test of several ideas that are still developing for me: how Application services and interfaces should evolve as the surface grows, whether domain/use-case organization remains intuitive at larger scale, and whether the additional Clean Architecture structure continues to justify its overhead once the project is delivering more complete features.
