# SquadSync Phase 2 — Core Domain, Persistence, and Application Boundary

**Project Retrospective** · SquadSync · Phase 2 — Core Domain and Persistence · October 2026

<details>
<summary>Metadata & related work</summary>

| Field | Value |
|---|---|
| Status | Complete |
| Sprint | Sprints 3–6 |
| Issue | Sprint trackers #82, #94, #102, #109 |
| Tags | domain-modeling, clean-architecture, ef-core, postgresql, dependency-inversion, integration-testing, agentic-workflow |
| Related Repo | https://github.com/bbubb/squadsync |
| Related Work | PRs #93, #101, #104, #107, #108, #112, #113 |

</details>

## Summary

Phase 2 moved SquadSync from an API foundation into a persisted domain model with its first real Application workflow.

The project now models Users, Teams, TeamMemberships with constrained TeamRoles, person-level PlayerProfiles, and team-specific RosterEntries. Those concepts are persisted through EF Core and PostgreSQL, tested both in isolation and against a real database, and exercised through an explicit Development demo scenario.

The larger outcome for me was architectural clarity. I already had experience with controllers, services, repositories, interfaces, and domain concepts. This phase made it much clearer how Clean Architecture turns some logical separations into physically enforced dependency boundaries, how domain modeling affects implementation structure, and how disciplined incremental validation can reduce uncertainty as the system grows.

## At a Glance

- Built the first persisted SquadSync domain model around teams, membership, player profiles, and roster state.
- Made Clean Architecture dependency boundaries concrete through separate .NET projects and the first Application-to-Infrastructure persistence contract.
- Used focused tests and real PostgreSQL validation to prove each small persistence slice before building on it.
- Saw the issue → Codex implementation → draft PR → human review workflow become a practical development and learning system.

## Key Themes / Reflections

### Clean Architecture made familiar layering physically enforceable

I was already comfortable with controller/service/repository-style layering, so separation of concerns was not new. The important shift was seeing how separate projects can constrain dependency direction rather than relying mainly on convention.

```text
API -> Application
API -> Infrastructure
Infrastructure -> Application
Infrastructure -> Domain
Application -> Domain
Domain -> no outer project
```

Application cannot casually reach into EF Core or PostgreSQL without changing the project dependency itself. The architecture can still be violated, but doing so becomes a visible design decision rather than an incidental reference.

This helped me distinguish logical layering from a stronger architectural boundary. It also sharpened the difference between being **domain-oriented** and following a more deliberately **domain-driven** implementation process. My earlier development experience already involved substantial domain thinking; the difference here is that business concepts and use cases more directly influence both structure and implementation order.

The trade-off is additional ceremony and cognitive overhead. More boundaries, contracts, tests, and files are not automatically better. I am interested to see whether the structure continues to earn that cost as Phase 3 introduces complete HTTP-to-database workflows.

### Modeling relationships and lifecycles clarified where rules belong

`TeamMembership` represents the relationship between a `User` and a `Team`, with one constrained `TeamRole`. Treating that relationship as its own concept reinforced an important lesson: sometimes the relationship between two things carries enough identity, state, or rules to deserve its own model.

A similar distinction emerged between `PlayerProfile` and `RosterEntry`. PlayerProfile contains person-level soccer attributes, while RosterEntry contains state that belongs to that person's membership on a particular team. Keeping those lifecycles separate made concepts such as domain state and relationship-specific state much less abstract.

The phase also produced a practical way to think about rule ownership:

- **Domain** owns business concepts, state, and rules intrinsic to those concepts.
- **Application** coordinates use cases and rules that require multiple pieces of domain state.
- **PostgreSQL** protects relational structure and uniqueness.
- **Infrastructure** implements the technical capabilities needed by the application.

The clearest example is roster eligibility. A RosterEntry can validate its own state, but it cannot know whether its related TeamMembership represents a Player. That cross-record rule belongs in the Application workflow:

```text
AddPlayerToRoster
    -> load TeamMembership
    -> require TeamRole.Player
    -> construct RosterEntry
    -> persist through an Application-owned contract
```

This was also the first concrete example in SquadSync that helped Dependency Inversion move beyond "use an interface" for me:

```text
AddPlayerToRoster
        -> IRosterPersistence
        <- EfRosterPersistence
```

The higher-level Application layer defines the capability its workflow needs; Infrastructure supplies the EF Core/PostgreSQL implementation. That mental model is clearer now, although I expect to keep refining interface ownership as the Application surface grows.

### Small validated slices started to justify their initial overhead

The implementation rhythm felt unusually granular at first. Small contracts were defined, implemented, tested, persisted, and validated before the next piece was added. For simple entities and relationships, that sometimes felt overly formal compared with designing a broader structure up front.

By the end of the phase, the pieces had started to compose into something more recognizable: a domain model, persistence layer, Application workflow, and repeatable demo scenario. The process is not strict test-first TDD; it is closer to defining intended behavior, implementing a small slice, adding focused tests, validating the relevant boundary, and merging only when there is evidence that the assumptions hold.

The real PostgreSQL tests were particularly useful because they proved behavior that isolated unit tests cannot: actual materialization, relationships, uniqueness, rollback behavior, and provider-specific persistence assumptions.

I have not concluded that this development rhythm is universally better. Its value so far is that later behavior is being built on components whose important assumptions have already been exercised. Phase 3 should be a better test of whether that rigor continues to pay off once the work produces more visible end-to-end features.

### The agentic workflow became a working engineering loop

The issue/PR workflow is probably the most novel part of my development process in this phase.

Issues define bounded intent and acceptance criteria, Codex performs scoped implementation and returns a draft PR, and I remain responsible for understanding the design, reviewing the tests and trade-offs, and deciding whether the work should merge.

That division of labor is beginning to feel less like using AI to generate code and more like an accelerated engineering workflow. Just as importantly, reviewing real implementations has helped architecture terminology become concrete. Concepts such as persistence, application orchestration, domain state, and dependency inversion are easier to understand when I can trace them through a real change and challenge the decisions during review.

## Why It Matters

Phase 2 moved SquadSync beyond scaffolding into real backend design: a persisted domain model, tested relational behavior, an Application workflow, and a repeatable development scenario.

The technical work demonstrates domain and relational modeling, Clean Architecture dependency boundaries, EF Core/PostgreSQL migrations, application orchestration, Dependency Injection, emerging Dependency Inversion understanding, and real-database integration testing.

For me, the more important growth is moving from recognizing familiar patterns such as layers, services, repositories, and interfaces toward understanding more precisely why those boundaries exist, what they cost, which direction dependencies should point, and how business concepts can drive implementation decisions.

## What's Next

Phase 3 will expose the completed team-and-roster model through a usable HTTP API.

That should provide a useful test of several ideas that are still developing for me: how Application services and interfaces evolve as the surface grows, whether domain/use-case organization remains intuitive at larger scale, and whether the additional Clean Architecture structure continues to justify its overhead once the project is delivering complete features.
