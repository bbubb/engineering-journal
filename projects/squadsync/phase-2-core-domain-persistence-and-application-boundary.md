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
| Tags | domain-modeling, clean-architecture, ef-core, postgresql, dependency-inversion, integration-testing, seed-data, agentic-workflow |
| Related Repo | https://github.com/bbubb/squadsync |
| Related Work | PRs #93, #101, #104, #107, #108, #112, #113 |

## Summary

Phase 2 moved SquadSync from an operational API foundation into a persisted domain with the first real Application workflow. The project now models represented people, teams, team membership, player profile data, and team-specific roster data; persists those concepts through EF Core and PostgreSQL; validates structural constraints against a real database; and includes an explicit Development-only demo scenario.

The most important outcome was not the number of entities added. Phase 2 clarified where different kinds of rules belong. Domain objects protect facts that are intrinsic to themselves. Application use cases enforce rules that require coordination across records. PostgreSQL enforces structural relationships and uniqueness. Infrastructure translates between those layers and the database/provider details.

The phase also made Clean Architecture more concrete. Earlier layered service/repository patterns were not discarded; the stronger improvement is that project references now enforce dependency direction and the higher-level Application layer owns the persistence capability it needs. The `AddPlayerToRoster` use case is the first complete example of that dependency inversion in working code.

## Context

Phase 1 ended with a .NET 10 modular API solution, PostgreSQL connectivity, Docker Compose, health checks, CI, and explicit API/Application/Domain/Infrastructure project boundaries. It intentionally stopped before EF Core persistence so the domain model could drive the database design rather than the reverse.

Phase 2 had to answer several questions:

- What does a `User` represent before authentication is introduced?
- How should a person participate in a team without over-generalizing the MVP?
- Which player data belongs to the person versus a specific team roster?
- Which rules belong in Domain, Application, Infrastructure, or PostgreSQL?
- How can EF Core persist guarded, EF-independent domain objects without making Domain depend on the ORM?
- When should a persistence abstraction be introduced instead of creating repositories for every entity by convention?

The implementation was intentionally incremental. Each new relationship was first defined as a domain contract, then implemented in Domain, then mapped in Infrastructure, and finally validated against real PostgreSQL.

## Key Ideas / Decisions

### Represent a person separately from future authentication

The MVP `User` represents a person known to SquadSync. It is not yet an account/login object.

That distinction prevents authentication concerns from defining the core soccer model prematurely. Future account/delegation behavior can be added around represented people without requiring Phase 2 to invent a login model before the product needs one.

### Make team participation an explicit relationship

`TeamMembership` represents the relationship between one `User` and one `Team`.

It contains one constrained `TeamRole`:

- Owner
- Coach
- AssistantCoach
- Manager
- Player
- Viewer

This made a relationship that matters to the product explicit instead of treating team participation as an incidental ORM join. PostgreSQL enforces that the referenced User and Team exist and that a User has only one membership per Team.

The model is deliberately narrower than a fully dynamic permission system. That reduces persistence and authorization complexity while preserving a clear extension point for later requirements.

### Separate person-level player data from team-context roster data

Phase 2 split the overloaded idea of a "player" into two concepts:

`PlayerProfile` belongs to the represented person and currently carries optional soccer attributes such as dominant foot, height, and weight.

`RosterEntry` belongs to a `TeamMembership` and carries team-specific state such as jersey number and roster status.

This matters because the data has different lifecycles. A person's dominant foot does not change when they join another team, while a jersey number or roster status can.

The model also avoids duplicating `UserId` and `TeamId` in `RosterEntry`; `TeamMembership` remains the single User-to-Team relationship.

### Separate domain truth, application truth, and database truth

A useful mental model emerged during the phase.

**Domain truth** is intrinsic to one object. Examples:

- IDs cannot be empty.
- `DominantFoot` and `RosterStatus` must be defined values.
- optional height/weight must be positive when supplied.
- a jersey number must follow the accepted label format.

**Application truth** requires other state. The key example is:

> only a `TeamMembership` whose role is `Player` may receive a `RosterEntry`.

A `RosterEntry` cannot know that by itself. The `AddPlayerToRoster` Application use case loads the membership, checks the role, constructs the Domain object, and then persists it.

**Database truth** protects relational structure:

- foreign keys point to existing principals;
- one `PlayerProfile` per User;
- one `RosterEntry` per TeamMembership;
- one TeamMembership per User/Team pair.

Keeping those categories separate prevented business rules from being hidden in database constraints or duplicated across layers.

### Keep EF Core and PostgreSQL in Infrastructure

The Domain project has no EF Core or Npgsql dependency. Infrastructure owns:

- `SquadSyncDbContext`;
- Fluent entity configurations;
- Npgsql/EF Core packages;
- migrations;
- concrete persistence adapters;
- PostgreSQL-specific exception handling.

Provider-specific code is expected in this layer. For example, `EfRosterPersistence` can inspect a PostgreSQL unique-violation exception and translate it into the Application-level `RosterEntryConflictException`.

The architectural rule is not "never mention PostgreSQL." It is "contain PostgreSQL knowledge at the Infrastructure boundary."

### Introduce persistence abstractions from use-case needs

Phase 2 did not create a repository for every entity.

The first persistence abstraction appeared when a concrete Application workflow needed one:

```text
AddPlayerToRoster
        ↓
IRosterPersistence
        ↑
EfRosterPersistence
        ↓
SquadSyncDbContext / PostgreSQL
```

`IRosterPersistence` is owned by Application because Application is the consumer defining the capability it requires. Infrastructure references Application and implements that contract.

This clarified Dependency Injection versus Dependency Inversion:

- Dependency Injection supplies the implementation at runtime.
- Dependency Inversion determines that the higher-level Application layer owns the abstraction and the lower-level Infrastructure layer implements it.

### Make demo data explicit and safe

Phase 2 closes with a Development-only `--seed-demo=true` command.

The seed path is:

- explicit rather than automatic on startup;
- idempotent through deterministic demo identifiers;
- non-destructive;
- limited to Development;
- transactional;
- able to detect conflicting reserved identifiers rather than silently overwriting data.

The seeder constructs Domain entities for data without corresponding creation use cases, but roster creation goes through `AddPlayerToRoster` so the demo path cannot bypass the business rule established in Application.

## Trade-offs

### Narrow MVP roles vs. generalized permissions

One constrained `TeamRole` per membership is much simpler than a dynamic role/permission system and is enough for the current soccer MVP.

The trade-off is that one person cannot currently hold multiple simultaneous roles on the same team through one membership. That is an accepted simplification rather than an accidental schema limitation.

### Guarded domain entities vs. ORM convenience

Domain entities use constructor guards and get-only state where practical. That makes invalid objects harder to create and keeps domain intent explicit.

The cost is that persistence mapping requires more deliberate EF configuration and validation that EF can materialize those objects correctly. Real PostgreSQL round-trip tests were used to prove that the domain model did not need to be weakened for the ORM.

### Restrictive deletes vs. automatic cascades

Relationships use restrictive/no-cascade delete behavior.

A membership is structurally dependent on a User and Team, but automatically cascading deletes could erase related history before the product has defined the correct deletion/archive workflow. Restricting deletes keeps that decision explicit for a future Application use case.

### Database-independent baseline tests vs. real PostgreSQL validation

Ordinary `dotnet test` remains database-independent. Real PostgreSQL tests are opt-in.

This preserves a fast baseline loop while still proving provider-specific behavior such as enum storage, uniqueness constraint names, materialization after clearing EF tracking, and transaction rollback against the actual database.

## What Changed / What I Learned

### Clean Architecture became an enforceable dependency model

The strongest lesson was that Clean Architecture is not primarily about renaming folders or rejecting familiar service/repository patterns.

The project references provide the hard boundary:

- Domain references no Infrastructure technology.
- Application references Domain.
- Infrastructure references Application and Domain.
- API acts as the composition/HTTP boundary.

Earlier layered designs already contained useful concepts such as services, repository interfaces, DTOs, and DI. The improvement here is ownership and compiler-enforced dependency direction.

### Domain navigation/relationships are not inherently persistence mistakes

Relationships and collections can legitimately belong in the Domain when the business model needs them. EF navigation properties are not inherently incompatible with Clean Architecture.

The useful test is whether a relationship exists because the domain needs to reason about it, rather than adding domain API surface solely for ORM convenience. This distinction is more useful than treating any particular C# property shape as "clean" or "unclean."

### Relationship objects can simplify important domain behavior

`TeamMembership` and `RosterEntry` demonstrate why a relationship sometimes deserves its own domain object.

When a relationship has identity, state, constraints, or lifecycle of its own, making it explicit can be clearer than hiding it inside collections or an ORM-generated join.

This does not mean every relationship should become an entity. The decision depends on whether the relationship itself carries business meaning.

### Infrastructure is where concrete technology belongs

EF Core mappings, Npgsql, PostgreSQL constraint names, and provider exceptions are not architecture violations when they stay in Infrastructure.

The boundary becomes valuable when Infrastructure translates those implementation details into concepts understood by higher layers instead of leaking them upward.

### Incremental persistence reduced debugging dimensions

The phase intentionally proved one relationship at a time:

1. User and Team;
2. TeamMembership and TeamRole;
3. PlayerProfile and RosterEntry;
4. the Application persistence boundary;
5. explicit demo seeding.

The visible feature output was modest, but the sequence avoided debugging domain meaning, EF mapping, relational constraints, application orchestration, and HTTP behavior simultaneously.

By the end of the phase, the pattern was proven enough that further work should move toward usable API behavior rather than continuing to create foundation slices without new risk.

## What Was Deferred / Open Questions

Phase 3 will expose roster-management behavior over HTTP. It still needs to decide the smallest coherent set of Team, membership, player-profile, and roster use cases/endpoints required for a usable coach workflow.

Authentication/account identity remains intentionally separate from represented `User` people.

Preferred positions remain deferred until the project defines the soccer position/formation/lineup vocabulary.

Three maintenance items remain open but do not block Phase 3:

- SquadSync #72: automate the local PostgreSQL/API smoke-validation sequence;
- SquadSync #89: investigate remaining Codex lifecycle/workflow inconsistencies;
- SquadSync #100: pin the supported .NET 10 SDK family with `global.json`.

## Lessons Learned

- **Different layers protect different kinds of truth.** Intrinsic object validity, cross-record workflow rules, and relational integrity should not be collapsed into one validation mechanism.
- **Dependency inversion becomes meaningful when a real use case needs a capability.** The first Application-owned persistence interface was easier to justify and size once `AddPlayerToRoster` existed.
- **Infrastructure should be concrete.** EF Core, Npgsql, PostgreSQL, migrations, and provider-specific error translation belong there.
- **Explicit relationships improve clarity when the relationship has behavior or state.** `TeamMembership` and `RosterEntry` are domain concepts, not just database join mechanics.
- **Clean Architecture is evolutionary, not ceremonial.** Familiar layered service/repository ideas can map cleanly into it when dependency ownership and project references are corrected.
- **Persistence tests should prove what unit tests cannot.** Real database validation is most valuable for materialization, provider conversions, FK/unique constraints, and rollback behavior.
- **Foundation work should stop when the pattern is proven.** Phase 3 should convert these boundaries into usable product behavior rather than repeating the same structural proof.

## Current Relevance

Phase 2 provides concrete portfolio evidence for backend, integration, cloud, technical systems, solutions engineering, and architecture-oriented work.

It demonstrates:

- domain modeling and scope discipline;
- modular/Clean Architecture dependency management;
- EF Core and PostgreSQL schema evolution;
- explicit relational modeling and constraint design;
- application-layer orchestration;
- Dependency Injection and Dependency Inversion in working code;
- provider-specific error translation at an Infrastructure boundary;
- unit versus real-database integration testing;
- safe local bootstrap/demo-data workflows;
- issue-backed, reviewable AI-assisted implementation.

These are also directly relevant to future cloud work. The same principle used here—higher-level code depending on capabilities while Infrastructure owns the provider—maps naturally to replacing or adding external services later without pushing provider details into core business logic.

## Next Steps

Return to the main SquadSync planning workflow and plan Phase 3 — Roster Management API.

Phase 3 should use the completed Phase 2 model to expose a small, coherent coach-facing API flow. Planning should determine which Team, membership, PlayerProfile, and roster operations are required first, define request/response and error semantics, and add HTTP integration tests without pulling authentication, frontend, match planning, or later service integrations forward prematurely.

The maintenance backlog should remain visible, but none of the current maintenance issues is a prerequisite for beginning Phase 3.
