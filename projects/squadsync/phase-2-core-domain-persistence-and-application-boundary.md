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

Phase 2 turned SquadSync's API foundation into a persisted domain model with its first real Application workflow.

The phase established the core team-and-roster concepts needed for the MVP: represented Users, Teams, TeamMemberships with constrained TeamRoles, person-level PlayerProfiles, and team-specific RosterEntries. Those concepts are persisted through EF Core and PostgreSQL, validated with both unit tests and opt-in real-database tests, and exercised through an explicit Development demo scenario.

For me, the more important outcome was conceptual. I already had experience thinking in controllers, services, repositories, interfaces, and domain concepts, but Phase 2 made several distinctions much clearer: logical separation versus physically enforced dependency boundaries, domain state versus persistence, intrinsic Domain rules versus Application-level workflow rules, and Dependency Injection versus Dependency Inversion. I do not feel that every one of those ideas is fully settled yet, but I understand the architecture far more concretely than I did at the beginning of the phase.

## Context

Phase 1 established the .NET 10 solution, Clean Architecture project boundaries, PostgreSQL connectivity, Docker Compose, CI, health checks, and testing baseline. Phase 2 needed to add real domain state without coupling the Domain project to EF Core or allowing persistence concerns to define the model.

The implementation rhythm felt unusually granular at first. Each slice defined a small contract, implemented a narrow part of the model, mapped it in Infrastructure, and proved it before moving outward. For relatively simple entities and relationships, that amount of formality sometimes felt disproportionate to the visible result.

By the end of the phase, however, those pieces had started to compose into something more recognizable: a domain model, persistence layer, first Application use case, and repeatable demo data. I am still evaluating the trade-off, but I can see the value in letting complexity grow on top of assumptions that have already been tested rather than framing most of the system first and discovering integration problems later.

This also feels different from the layered service architecture I was more familiar with. The earlier mental model was not devoid of domain thinking or separation of concerns; many of the same design principles were present. What is newer to me is the combination of Clean Architecture's explicit project boundaries with a more deliberately domain-driven implementation process, where business concepts and use cases increasingly determine the structure and sequencing of the work.

## Key Decisions

### Model team participation explicitly

`TeamMembership` became the relationship between a represented `User` and a `Team`, with one constrained `TeamRole`.

This keeps the MVP simple while making team participation a first-class concept instead of an incidental database join. PostgreSQL enforces the structural relationship and uniqueness of one membership per User/Team pair.

This relationship object also helped clarify something I had understood more loosely before: sometimes the relationship between two concepts carries enough identity, state, or rules that it deserves to be modeled explicitly rather than hidden inside surrounding objects.

### Separate player identity from roster context

`PlayerProfile` stores person-level soccer attributes, while `RosterEntry` stores data that belongs to a player's membership on a specific team.

That distinction prevents team-specific state such as jersey number or roster status from being mixed with attributes of the person. `RosterEntry` points to `TeamMembership`, so the model does not duplicate the User-to-Team relationship.

This separation was one of the places where the terminology began to make the architecture easier for me to reason about. "Domain state," "persistence," and "relationship-specific state" are becoming less abstract as I can point to concrete examples in the model.

### Put different rules in the layer that can actually know them

Phase 2 produced a practical rule-of-thumb that I expect to reuse:

- **Domain** protects business concepts, state, and rules intrinsic to those concepts.
- **Application** orchestrates use cases and rules that require coordination across domain state.
- **PostgreSQL** protects relational structure and uniqueness.
- **Infrastructure** implements the technical capabilities needed by the application.

The clearest example is roster eligibility. A `RosterEntry` can validate its own ID, jersey number, and status, but it cannot know whether its membership is actually a Player membership.

That rule therefore lives in the `AddPlayerToRoster` Application use case:

```text
AddPlayerToRoster
    -> load TeamMembership
    -> require TeamRole.Player
    -> construct RosterEntry
    -> persist through an Application-owned contract
```

I understood the general idea of business logic and technical layers before this phase, but the distinction between a Domain rule and an Application workflow rule is becoming much sharper through examples like this.

### Keep persistence technology in Infrastructure

EF Core, Npgsql, migrations, provider-specific constraint handling, and PostgreSQL exception translation remain in `SquadSync.Infrastructure`.

The Domain project has no EF Core dependency. Application depends on the capability it needs, while Infrastructure implements that capability with EF Core and PostgreSQL.

The important lesson for me was not that PostgreSQL-specific code is somehow undesirable. It has to live somewhere. The point is that the concrete technology stays at the Infrastructure boundary instead of becoming something the core business code must understand.

## What Changed / What I Learned

### Clean Architecture became a structural concept instead of only a logical one

I had already worked with controller/service/repository-style separation, so the idea of layers was not new to me. What became clearer during this phase was the difference between organizing code into logical layers and physically enforcing dependency direction.

The current project references make that visible:

```text
API -> Application
API -> Infrastructure

Infrastructure -> Application
Infrastructure -> Domain

Application -> Domain

Domain -> no outer project
```

Application cannot accidentally reach into EF Core or PostgreSQL without first changing the project dependency itself. That does not make the architecture impossible to violate, but it makes the violation deliberate and obvious rather than an incidental reference.

I am still adapting to the codebase being organized both by architectural boundary and increasingly by domain/use-case concepts. A more traditional layered service structure still feels immediately familiar to me. I am interested to see whether the current approach feels clearer or more fragmented once Phase 3 introduces complete HTTP-to-database workflows.

### Dependency Inversion is becoming clearer, but it is still a developing mental model

`AddPlayerToRoster` gave me the first concrete example in this project of an Application workflow depending on a persistence capability rather than on EF Core directly.

```text
AddPlayerToRoster
        -> IRosterPersistence
        <- EfRosterPersistence
```

What I am beginning to understand is that interface ownership is less about "put the interface next to whoever calls it" and more about which layer defines the policy or capability. Application defines what it needs in order to execute the use case; Infrastructure supplies the technical implementation.

I understand that example better now than I did before implementing it, but I do not consider Dependency Inversion fully intuitive yet. I expect that understanding to improve as more Application services and external capabilities appear.

### Testing small slices felt slow until the pieces started composing

The project is not following strict test-first TDD. The pattern has been closer to:

```text
define intended behavior
-> implement a small slice
-> add focused tests
-> validate the relevant boundary
-> merge only with evidence
```

At first, proving such small pieces sometimes felt almost trivial. By the end of Phase 2, I could see why the accumulated evidence matters: Domain behavior, EF materialization, migrations, PostgreSQL constraints, Application orchestration, and demo seeding were each proven before the next layer depended on them.

The next question is whether this continues to pay off as the application becomes more feature-rich. Phase 3 should be a better test because the work will begin producing complete API workflows instead of mostly foundational slices.

### The human-guided agentic workflow is becoming genuinely useful

The issue/PR workflow is probably the most novel part of my development process during this phase.

Issues define bounded intent and acceptance criteria, Codex implements scoped work and returns a draft PR, and I remain responsible for understanding the architecture, reviewing the changes and tests, and deciding whether to merge.

That division of labor is beginning to feel less like "using AI to generate code" and more like an accelerated engineering workflow. It also seems to help my learning: concepts such as domain state, persistence, application orchestration, and dependency inversion become easier to understand when I can trace them through a real implementation and challenge the decisions during review.

If this continues to work as the system becomes more complex, it could materially increase both my production capacity and the amount I can learn while building.

### Persistence is more than "the entity saved"

The PostgreSQL tests validate behavior that unit tests alone cannot prove: actual materialization, relationships, uniqueness, rollback behavior, and provider-specific persistence assumptions.

That gave the phase a stronger definition of "working persistence" than simply calling `SaveChangesAsync`.

## Trade-offs

The MVP intentionally favors explicit, narrow concepts over a generalized role or authorization system. One TeamMembership currently has one TeamRole, which is sufficient for the current coach/roster workflow but may need to evolve as requirements become more complex.

The architectural process itself also has a trade-off. Small, heavily validated slices create more ceremony and can make progress feel slow or less visually organized than designing a larger structure up front. The potential benefit is that each new level of complexity is built on a foundation that has already been exercised. I have not reached a final judgment on that balance yet.

Ordinary tests remain database-independent, while real PostgreSQL validation is opt-in. This keeps the fast development loop lightweight while still providing provider-specific evidence when schema and persistence behavior matter.

## Lessons Learned

- Logical layering and Clean Architecture can share many of the same design principles, but CA project boundaries make dependency direction more explicit and enforceable.
- Being domain-oriented is not quite the same as letting the domain drive implementation order and boundaries.
- A domain model is easier to reason about when person-level data and relationship-specific data have separate lifecycles.
- Business rules should live where the information needed to enforce them is available.
- Infrastructure can and should be concrete; the architectural goal is to prevent technical details from leaking inward.
- Dependency inversion makes more sense to me when it emerges from a real use case rather than from an abstract repository pattern.
- A small slice is not finished merely because it compiles; the validation should prove the assumptions that matter at that boundary.
- The issue -> implementation -> draft PR -> human review loop is becoming a productive way to combine AI acceleration with human ownership and learning.
- Foundation work should stop once the pattern is proven. The next phase should turn these boundaries into usable product behavior.

## Current Relevance

For my portfolio, Phase 2 demonstrates practical backend and architecture skills rather than only framework familiarity:

- domain and relational modeling;
- Clean Architecture dependency boundaries;
- EF Core and PostgreSQL migrations;
- application-layer orchestration;
- dependency injection and developing understanding of dependency inversion;
- integration testing against a real database;
- safe development bootstrap workflows;
- human-guided, issue-backed AI-assisted development.

More importantly, this phase reflects a shift in how I understand architecture. I am moving from recognizing patterns such as layers, services, repositories, and interfaces toward understanding why their boundaries exist, which direction dependencies should point, and how the business domain can drive implementation decisions.

That is directly relevant to the backend, integration, cloud, systems, solutions-engineering, and architecture-oriented roles I am targeting.

## Next Steps

Phase 3 will expose the completed team-and-roster model through a usable HTTP API.

The next planning step is to define the smallest coherent set of Team, membership, PlayerProfile, and roster operations needed for a coach-facing workflow, including request/response validation, consistent errors, and HTTP integration tests.

I also expect Phase 3 to clarify several concepts that are still developing for me: where Application services/interfaces should live as the surface grows, how domain/use-case organization feels at larger scale, and whether the additional Clean Architecture structure continues to justify its overhead once the project is delivering more complete features.

Authentication, frontend work, match planning, and later service integrations remain outside that immediate scope.
